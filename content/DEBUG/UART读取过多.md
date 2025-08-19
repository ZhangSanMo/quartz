# 代码运行不符合预期

用户反馈调用`uart_read`返回值会超过传入的`len`值，导致后续程序出错。

# 确认情况

医生对于病人的口述应当持部分怀疑态度，所以首先应当确定实际状况。

1. 先检查代码

```c
int32 uart_read(uint32 uart_channel, uint8 *buf, uint32 len)
{
	uint32 res = 0U;
	uint32 temp_len;
	int32 i;
	uint16 *pbuf = (uint16 *)buf;
	if (len > 256)
		len = 256;
	if ((uart_channel < lpuartCfg.baselen) && (uart_channel > 0))
	{
		temp_len = UARTMIN(len, RingBuffReadSpace(&uartRingBuff[uart_channel]));
		ReadRingBuff(&uartRingBuff[uart_channel], buf, temp_len);
		if(uartTransWidth[uart_channel] == 2)
		{
			for(i = 0; i < temp_len/sizeof(uint16); i++)
			{
				pbuf[i] = 0x3FFU & pbuf[i];
			}
		}

		res = temp_len;
	}
	return res;
}
```

2. 添加打印

从代码上观察是不应该出现这种情况的，在返回部分添加日志记录`res, len和temp_len`

3. 确定情况

    1. 和用户确认这部分程序调用不在中断里面，先暂时排除中断带来的问题
    2. 打印显示确实出现`res > len`的情况

# 发现疑点

通过阅读代码可知，正常逻辑下是不可能出现`res > len`的情况的，`res`由`temp_len = UARTMIN(len, RingBuffReadSpace(&uartRingBuff[uart_channel]));`这部分决定。

那么问题可能出现在`UARTMIN`或者`RingBuffReadSpace`中，但是考虑到`UARTMIN`的功能，`RingBuffReadSpace`不会导致返回值大于`len`的情况，所以问题只可能出现在`UARTMIN`这里。

# 来自宏的恶意

```c
#define UARTMIN(a, b) (((a) < (b)) ? (a) : (b))
```

这个宏简单到不可能出问题，但是没有别的可能了。只能询问ChatGPT，但是还是需要一定询问技巧的

    #define UARTMIN(a, b) (((a) < (b)) ? (a) : (b))这个宏存在什么潜在问题？

如果直接问什么问题，ChatGPT回答的是有问题的。

    这个宏定义看起来是用于比较两个值，并返回其中较小的值。然而，它存在一些潜在的问题：

    缺乏错误检查：该宏没有对输入参数进行任何验证或错误检查。如果传递给宏的参数不是数值类型或者有其他问题，可能会导致未定义的行为。

    副作用：宏中使用了两次参数 a 和 b，如果这些参数在执行过程中具有副作用（例如是函数调用），则可能会导致意外的行为。例如，如果传递给宏的参数是 UARTMIN(i++, j++)，由于宏展开时参数会被替换两次，可能会导致 i 和 j 的值发生不可预测的变化。

    可能会多次计算参数：宏展开后，参数可能会被多次计算，这可能会导致意外的结果。例如，如果一个参数是一个表达式 UARTMIN(foo(), bar())，那么 foo() 和 bar() 可能会被分别计算两次。

    为了解决这些潜在问题，建议使用函数代替宏来实现同样的功能。函数可以提供更好的类型安全性和错误检查，并且可以避免上述副作用和多次计算参数的问题。

所以问题很明显了，宏展开里面`RingBuffReadSpace`被调用了两次，两次返回值第一次小于`len`，第二次大于`len`。


# 总结

1. 除了中断，宏的展开同样隐蔽
2. 尽量用内联替代宏