Logical Address ， Linear Address ， Physical Address

Linear Address 是转换过程中出现的中间态而已。虚拟内存中和代码上能看到的内存地址一般都是Logiccal Address （带冒号的除外）。

这几个地址是如何转换的：

首先拿到一个Logical Address，然后去查GDT表，根据选择子找到Base，再把base 和 Offset加一下就是Linear Address，最主要的部分就是如何从Linear Adress 找到 对应的Physical Address。如下图所示把Linear Address 分成三段，前10为时dir，中间10位是Table，最后12位是offset。根据dir去查找一级页表，即页表目录，页表的基值放在CR3（页表基值寄存器）寄存器中，找到之后就可以读取出PDE，通过PDE的前20位VPN找到二级页表，以Table作为偏移找出PTE，将PTE中的前20位PPN加上Offset就找到了对应的物理地址。

![](../../image/address\-translation/L\-L\-P\-Address.jpg)

操作系统是一个复杂的系统，

操作的系统的演变应该从8086芯片讲述到80386芯片可能才算是入门吧

8086

