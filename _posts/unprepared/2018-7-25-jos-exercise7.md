---
layout: post
title: jos-exercise6
categories: [操作系统]
---
在执行开启分页之前的内存相关位置的内容
{%highlight ruby%}
Breakpoint 1, 0x0010001d in ?? ()
(gdb) x 0x100000
   0x100000:	add    0x1bad(%eax),%dh
(gdb) 
   0x100006:	add    %al,(%eax)
(gdb) 
   0x100008:	decb   0x52(%edi)
(gdb) x 0xf0100000
   0xf0100000 <_start+4026531828>:	add    %al,(%eax)
(gdb) 
   0xf0100002 <_start+4026531830>:	add    %al,(%eax)
(gdb) 
   0xf0100004 <_start+4026531832>:	add    %al,(%eax)
{%endhighlight%}

在执行开启分页之后内存的相关位置

{%highlight ruby%}
(gdb) x 0xf010000c
   0xf010000c <entry>:	movw   $0x1234,0x472
(gdb) x 0x10000c
   0x10000c:	movw   $0x1234,0x472
(gdb) 

{%endhighlight%}

由此可见高地址内存已经被映射到低地址