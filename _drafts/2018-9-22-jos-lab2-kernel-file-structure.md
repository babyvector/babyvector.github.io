---
layout: post
title: 操作系统|jos|lab2|kernel-elf-structure
categories: [操作系统]
---

#kernel进入操作系统之后的整体结构
root@ubuntu:/home/xuyongkang/6.828/lab/obj/kern# readelf -a kernel
ELF Header:
  Magic:   7f 45 4c 46 01 01 01 00 00 00 00 00 00 00 00 00 
  Class:                             ELF32
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              EXEC (Executable file)
  Machine:                           Intel 80386
  Version:                           0x1
  Entry point address:               0x10000c
  Start of program headers:          52 (bytes into file)
  Start of section headers:          102344 (bytes into file)
  Flags:                             0x0
  Size of this header:               52 (bytes)
  Size of program headers:           32 (bytes)
  Number of program headers:         3
  Size of section headers:           40 (bytes)
  Number of section headers:         11
  Section header string table index: 8

Section Headers:
  [Nr] Name              Type            Addr     Off    Size   ES Flg Lk Inf Al
  [ 0]                   NULL            00000000 000000 000000 00      0   0  0
  [ 1] .text             PROGBITS        f0100000 001000 003771 00  AX  0   0 16
  [ 2] .rodata           PROGBITS        f0103780 004780 0013f8 00   A  0   0 32
  [ 3] .stab             PROGBITS        f0104b78 005b78 005b5d 0c   A  4   0  4
  [ 4] .stabstr          STRTAB          f010a6d5 00b6d5 001ee6 00   A  0   0  1
  [ 5] .data             PROGBITS        f010d000 00e000 00a300 00  WA  0   0 4096
  [ 6] .bss              NOBITS          f0117300 018300 000650 00  WA  0   0 32
  [ 7] .comment          PROGBITS        00000000 018300 000035 01  MS  0   0  1
  [ 8] .shstrtab         STRTAB          00000000 018f7a 00004c 00      0   0  1
  [ 9] .symtab           SYMTAB          00000000 018338 0007e0 10     10  54  4
  [10] .strtab           STRTAB          00000000 018b18 000462 00      0   0  1
Key to Flags:
  W (write), A (alloc), X (execute), M (merge), S (strings)
  I (info), L (link order), G (group), T (TLS), E (exclude), x (unknown)
  O (extra OS processing required) o (OS specific), p (processor specific)

There are no section groups in this file.

Program Headers:
  Type           Offset   VirtAddr   PhysAddr   FileSiz MemSiz  Flg Align
  LOAD           0x001000 0xf0100000 0x00100000 0x0c5bb 0x0c5bb R E 0x1000
  LOAD           0x00e000 0xf010d000 0x0010d000 0x0a300 0x0a950 RW  0x1000
  GNU_STACK      0x000000 0x00000000 0x00000000 0x00000 0x00000 RWE 0x10

 Section to Segment mapping:
  Segment Sections...
   00     .text .rodata .stab .stabstr 
   01     .data .bss 
   02     

There is no dynamic section in this file.

There are no relocations in this file.

The decoding of unwind sections for machine type Intel 80386 is not currently supported.






