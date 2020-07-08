---
layout: post
title: elf文件构成
categories: [操作系统]
---

##jos中的有关elf文件

一般用于获取elf文件的详细信息的两个命令

objdump -h boot.out//获得elf文件的各个段的信息

readelf -h boot.out//获得elf文件的头部信息


![](../image/avl-rotation/elf-file-structrue.jpg)



.text  程序可执行命令
.data  静态内存分配 初始化的全局变量段（数据部分，保存程序的初始化数据，例如用int x = 5等初始化程序声明的全局变量）
.rodata （只读数据，例如C编译器生成的ASCII字符串常量）
.bss	静态内存分配，未初始化的全局变量段或者初始化为0的全局变量段
.stab	调试信息段
.stabstr	
