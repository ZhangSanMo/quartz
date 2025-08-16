# IAR下的下载流程
## 1. 配置.board文件
该文件用于配置不同section和区域的程序用不同的flashloader处理。
![[Pasted image 20241125132357.png]]

- 每个 pass 表示所需要烧录的一个段；
- loader 表示该段烧录对应的.flash文件；
- range 表示该段在输出文件 .out 中对应的地址范围；
- abs_offset 表示烧写的Flash基地址；
- args 表示烧写的输入参数

## 2. 配置.flash文件
![[Pasted image 20241125132625.png]]

.flash 文件指定了：
- exe : 使用的Flashloader的程序；
- macro : Flashloader在加载前执行的IAR脚本；
- page : 该flash区域的page size；
- block : 该flash区域的block size，格式为，n(十进制)，m(16进制)，n表示block数量，m表示每
个block的大小；
- flash_base : 该flash区域的基地址，需要与.board中保持一致

## 3. 编译flashloader.out

flashloader.out也是一段程序，一般由IAR或者厂商提供，它能处理.board文件传入的参数和IAR传入的ELF文件等。

## 4. 完成下载


