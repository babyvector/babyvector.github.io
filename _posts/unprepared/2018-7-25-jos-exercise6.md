---
layout: post
title: jos-exercise6
categories: [操作系统]
---

#在bootloader之前的0x100000以及以下的几个位置是中的内容是:
{%highlight ruby%}
(gdb) x 0x100000
   0x100000:	add    %al,(%eax)
(gdb) 
   0x100002:	add    %al,(%eax)
(gdb) 
   0x100004:	add    %al,(%eax)
(gdb) 
   0x100006:	add    %al,(%eax)
(gdb) 
   0x100008:	add    %al,(%eax)
(gdb) 
   0x10000a:	add    %al,(%eax)
(gdb) 
   0x10000c:	add    %al,(%eax)
(gdb) 
   0x10000e:	add    %al,(%eax)
{%endhighlight%}

##而在bootloader执行完毕之后0x100000位置以及其之后的相关位置变成了：
{%highlight ruby%}
(gdb) x 0x100000
   0x100000:	add    0x1bad(%eax),%dh
(gdb) 
   0x100006:	add    %al,(%eax)
(gdb) 
   0x100008:	decb   0x52(%edi)
(gdb) x 0x100002
   0x100002:	lods   %ds:(%esi),%eax
//其中：
0x10000c的位置为：
(gdb) x 0x10000c
   0x10000c:	movw   $0x1234,0x472
//这是内核中的第一条指令
{%endhighlight%}