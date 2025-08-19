# 问题反馈
应用展示：RX8111无法获取时间

# 问题确认和现象记录

- RX8111无法获取时间
- 测量波形发现SDA没有波形

# 预设可能问题并验证

1. 硬件层面连错线

    检验措施：将管脚配置为GPIO, 手动控制，观察示波器波形是否正常反应

    结果： 可以正常反应

2. GPIO无配置

   检验措施：检查设备树，检查上电打印

   结果： 确认存在

3. GPIO重复配置

    检查措施：搜索关键寄存器，检查是否存在别处
    结果： 没有存在其他处配置

4. I2C配置其他GPIO
   
   注：硬件描述了之前遇到过的一个问题，上层将GPIO2配置为I2C2的功能引脚，而在底层软件中，又将I2C2的功能引脚配置为GPIO5，因此对于问题3是检查不出来的。相当于GPIO无配置和GPIO重复配置的中间问题：GPIO错误配置， 配置了但是配错了。

   检查措施： 搜索I2C3可能的其他配置GPIO, 检查是否I2C管脚功能生效
   结果：其他可配置为I2C管脚的并没有生效

5. GPIO引脚输出能力
   
   检查措施：I2C的信号线需要上拉，检查管脚输出配置是否匹配
   结果：确实没有上拉。但是修改为上拉后只是变成了高电平，但是仍然没有变化。

6. 时钟信号错误
   
   注：时钟信号错误按理来说应该不会导致管脚输出没变化，只会导致通讯故障。
   检查措施： 检查时钟配置clockConfig.c，发现时钟应该为24MHz而不是48MHz。但是修改后管脚输出仍没变化。

# 预设问题全部MISS

以上问题全部未命中，只能再次观察现象。对比观察之前主机的配置文件。

```c
/* 硬件A配置 */
"@pinmux@["
"&muxcfg&<0x40C08024,0x6,0x40C08084,0x1,0x40C08064,1,0x10>," /*IOMUXC_GPIO_LPSR_09_LPI2C5_SCL*/
"&muxcfg&<0x40C08020,0x6,0x40C08088,0x1,0x40C08060,1,0x10>," /*IOMUXC_GPIO_LPSR_09_LPI2C5_SDA*/
"]"
"@i2c5@[ &init&<1>,&clock&<48000000>,&baudRate&<100000>]"
"@rx8111ce@[ &init&<1>,&date&<2021,12,27>,&time&<19,11,12>,&i2cchannel&<5>]"

/* 硬件B配置 */
"@pinmux@["
/* IOMUXC_GPIO_DISP_B2_10_LPI2C3_SCL  */
"&muxcfg&<0x400E823C,0x6,0x400E85BC,0x1,0x400E8480,0,0x10>,"
/* IOMUXC_GPIO_DISP_B2_11_LPI2C3_SDA  */
"&muxcfg&<0x400E8240,0x6,0x400E85C0,0x1,0x400E8484,0,0x10>,"
"]"
"@i2c3@[ &init&<1>,&clock&<24000000>,&baudRate&<100000>]"
"@rx8111ce@[ &init&<1>,&date&<2021,12,27>,&time&<19,11,12>,&i2cchannel&<3>]"
```

似乎只有i2c5和i2c3的区别，但是发现管脚配置的倒数第二个，一个是1，一个是0，这让我想起当时QSPI的一个DQS管脚，这位必须设置为1，才能既保持输出功能又具备输入功能。

# 验证

修改管脚配置倒数第二个，即SION位。重新测试，成功。

> Software Input On Field.
> 
> Force the selected mux mode Input path no matter of MUX_MODE functionality.
> 
> 0 - Input Path is determined by functionality
> 
> 1 - Force input path of pad GPIO_DISP_B2_10

# 总结

- 由于采用了脚本生成配置的方法，因此还是需要对各个配置项逐个检查，并且对各个配置项的机理有了解。
- GPIO配置检查错误经验+1

重温一下GPIO配置项
`"&muxcfg&<0x400E823C,0x6,0x400E85BC,0x1,0x400E8480,1,0x10>,"`其中

- 0x400E823C寄存器负责管脚的SION位和功能选择
- 0x6指示了功能选择
- 0x400E85BC指示了LPI2C3_LPI2C_SCL_SELECT_INPUT，设定了I2C3使用哪套GPIO
- 0x1, LPI2C3_LPI2C_SCL_SELECT_INPUT设定选择GPIO_DISP_B2_10
- 1, SION位置位，既可以输出也可以输入
- 0x10, 其实等同于0x0，指定管脚输出能力
