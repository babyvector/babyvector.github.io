---
layout: post
title: 操作系统-大小端问题解读
categories: [操作系统]
---

####来源见下链接

[这个大小端讲解的非常好(通知即删)](https://jocent.me/2017/07/25/big-little-endian.html)


##大小端存储模式精解

计算机系统中内存是以字节为单位进行编址的，每个地址单元都唯一的对应着1个字节（8 bit）。这可以应对char类型数据的存储要求，因为char类型长度刚好是1个字节，但是有些类型的长度是超过1个字节的（字符串虽然是多字节的，但它本质是由一个个char类型组成的类似数组的结构而已），比如C/C++中，short类型一般是2个字节，int类型一般4个字节等。因此这里就存在着一个如何安排多个字节数据中各字节存放顺序的问题。正是因为不同的安排顺序导致了大端存储模式和小端存储模式的存在。


### 1. 概述
####1.1 定义
假如有一个4字节的数据为 0x12 34 56 78（十进制：305419896，0x12为高字节，0x78为低字节），若将其存放于地址 0x4000 8000中，则有：

内存地址	0x4000 8000（低地址）	0x4000 8001	0x4000 8002	0x4000 8003（高地址）
大端模式	0x12（高字节）	0x34	0x56	0x78（低字节）
小端模式	0x78（低字节）	0x56	0x34	0x12（高字节）
明显可以看出规律，即大端“高低低高”，小端“高高低低”：

大端模式：是指数据的高字节保存在内存的低地址中，而数据的低字节保存在内存的高地址中
小端模式，是指数据的高字节保存在内存的高地址中，而数据的低字节保存在内存的低地址中
1.2 特点
为什么截然相反的大小端存储模式能够并存至今？在标准化备受推崇的今天，为什么大小端谁都没有被另外一个所同化？我想这除了历史的惯性使然，还与它们各自的优缺点有关。

大端模式优点：符号位在所表示的数据的内存的第一个字节中，便于快速判断数据的正负和大小

小端模式优点：1. 内存的低地址处存放低字节，所以在强制转换数据时不需要调整字节的内容（注解：比如把int的4字节强制转换成short的2字节时，就直接把int数据存储的前两个字节给short就行，因为其前两个字节刚好就是最低的两个字节，符合转换逻辑）；2.  CPU做数值运算时从内存中依顺序依次从低位到高位取数据进行运算，直到最后刷新最高位的符号位，这样的运算方式会更高效

其各自的优点就是对方的缺点，正因为两者彼此不分伯仲，再加上一些硬件厂商的坚持（见1.3节），因此在多字节存储顺序上始终没有一个统一的标准

1.3 现状
Intel的80×86系列芯片使用小端存储模式
ARM芯片默认采用小端，但可以切换为大端
MIPS芯片采用大端，但可以在大小端之间切换
在网络上传输的数据普遍采用的都是大端