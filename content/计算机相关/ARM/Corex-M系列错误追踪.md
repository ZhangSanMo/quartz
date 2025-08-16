# 核心代码

```c
    /* copy called function address */

    for (; sp < stack_start_addr + stack_size; sp += sizeof(int32))

    {

        /* the *sp value may be LR, so need decrease a word to PC */

        pc = *((uint32 *)sp) - sizeof(int32);

        /* the Cortex-M using thumb instruction, so the pc must be an odd number */

        if (pc % 2 == 0)

        {

            continue;

        }

        /* fix the PC address in thumb mode */

        pc = *((uint32 *)sp) - 1;

        if ((pc >= code_start_addr + sizeof(int32)) && (pc <= code_start_addr + code_size) && (depth < CMB_CALL_STACK_MAX_DEPTH)

            /* check the the instruction before PC address is 'BL' or 'BLX' */

            && usrDisassemInsIsBlBlx(pc - sizeof(int32)) && (depth < size))

        {

            /* the second depth function may be already saved, so need ignore repeat */

            if ((depth == 2) && regs_saved_lr_is_valid && (pc == buffer[1]))

            {

                continue;

            }

            buffer[depth++] = pc;

        }

    }
```
# 核心思想

1. 遍历栈上数据
2. 找到调用函数过程中被压入栈的PC值
	1. 要求bit0位为1。这是因为M系列只支持Thumb模式
	2. 判断对应PC地址是否为BLX或者BL指令
3. 记录对应PC链

# 知识点

1. EPSR.T标志是处于ARM状态或者Thumb状态。对PC的几个操作会导致该位更新：
	1.  BLX或者BX
	2.  LDR到PC
	3. POP或者LDM包括PC

2. CPU的指令并不是0x0112，0x01表示指令1, 0x12表示参数。而是一个紧凑的二进制。这可能由于汇编语言`MOV A, B`的写法导致的印象。实际上的指令：
![[Pasted image 20240805163912.png]]
![[Pasted image 20240805163953.png]]

# 思维误区

前面的`pc = *((uint32 *)sp) - sizeof(int32);`已经减过一次值了，后续判断是否为BL或者BLX时又减了一次值。实际上中间`pc = *((uint32 *)sp) - 1;`重新对PC进行赋值，并没有减4。
误区：不是所有代码都是完全优化的，有些可能仅仅用于理解。