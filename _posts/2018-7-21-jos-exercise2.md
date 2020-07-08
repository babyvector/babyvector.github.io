---
layout: post
title: jos-练习2
categories: [操作系统]
---

Exercise 3. Take a look at the lab tools guide, especially the section on GDB commands. Even if you're familiar with GDB, this includes some esoteric GDB commands that are useful for OS work.

Set a breakpoint at address 0x7c00, which is where the boot sector will be loaded. Continue execution until that breakpoint. Trace through the code in boot/boot.S, using the source code and the disassembly file obj/boot/boot.asm to keep track of where you are. Also use the x/i command in GDB to disassemble sequences of instructions in the boot loader, and compare the original boot loader source code with both the disassembly in obj/boot/boot.asm and GDB.

Trace into bootmain() in boot/main.c, and then into readsect(). Identify the exact assembly instructions that correspond to each of the statements in readsect(). Trace through the rest of readsect() and back out into bootmain(), and identify the begin and end of the for loop that reads the remaining sectors of the kernel from the disk. Find out what code will run when the loop is finished, set a breakpoint there, and continue to that breakpoint. Then step through the remainder of the boot loader.
```

为什么会有这一步
readseg((uint32_t) ELFHDR, SECTSIZE*8, 0);
为什么是第一页，是8个扇区而不是一个简单的头部


```
Be able to answer the following questions:

###Q:At what point does the processor start executing 32-bit code? What exactly causes the switch from 16- to 32-bit mode?
#####A:mov %eax,%cr0  ljmp $0xb866,$0x87c32

###Q:What is the last instruction of the boot loader executed, and what is the first instruction of the kernel it just loaded?
#####A:
Where is the first instruction of the kernel?
How does the boot loader decide how many sectors it must read in order to fetch the entire kernel from disk? Where does it find this information?