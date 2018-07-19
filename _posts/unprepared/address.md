Logical Address ， Linear Address ， Physical Address

Linear Address 是转换过程中出现的中间态而已。虚拟内存中和代码上能看到的内存地址一般都是Logiccal Address （带冒号的除外）。

这几个地址是如何转换的：

首先拿到一个Logical Address，然后去查GDT表，根据选择子找到Base，再把base 和 Offset加一下就是Linear Address，最主要的部分就是如何从Linear Adress 找到 对应的Physical Address。如下图所示把Linear Address 分成三段，前10为时dir，中间10位是Table，最后12位是offset。根据dir去查找一级页表，即页表目录，页表的基值放在CR3（页表基值寄存器）寄存器中，找到之后就可以读取出PDE，通过PDE的前20位VPN找到二级页表，以Table作为偏移找出PTE，将PTE中的前20位PPN加上Offset就找到了对应的物理地址。

![](../../image/address\-translation/L\-L\-P\-Address.jpg)

操作系统是一个复杂的系统，

操作的系统的演变应该从8086芯片讲述到80386芯片可能才算是入门吧

8086

首先是8086的寄存器
AX
BX
CX
DX

CS
DS
ES

BP
SP
IP

SI
DI
这些寄存器都是16位另外其CPU的地址总线是20位的，并采用 段基址：偏移量的 -》 段基址*16+偏移量组合成为地址其寻址范围为：
0-0x10ffef而且8086中之后CPU的实模式而不存在保护模式

而且在1M的RAM中0-640KB才是整个系统和用户能使用的空间之上一直到1M的空间范围内是VGA映射，各种硬件的映射和BIOS的硬连线的地址

在经过80286之后x86CPU结构在进入80386芯片之后基本上算是定型




