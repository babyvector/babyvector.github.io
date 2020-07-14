---
layout: post
title: jos-练习1
categories: [操作系统]
---

**Exercise 1**. Familiarize yourself with the assembly language materials available on the 6.828 reference page. You don't have to read them now, but you'll almost certainly want to refer to some of this material when reading and writing x86 assembly.

We do recommend reading the section "The Syntax" in [Brennan's Guide to Inline Assembly](http://www.delorie.com/djgpp/doc/brennan/brennan_att_inline_djgpp.html). It gives a good (and quite brief) description of the AT&T assembly syntax we'll be using with the GNU assembler in JOS.

##The Syntax
So, DJGPP(DJGPP是一个编译器和一组工具，可以让您生成在MS-DOS / MS-Windows机器上运行的32位保护模式程序。) uses the AT&T assembly syntax(使用AT&T语法). What does that mean to you?
Register naming:

##### 寄存器前面加%. 为了引用eax:

	- AT&T:  %eax
	- Intel: eax

##### 源和目的的顺序:

In AT&T syntax (which is the UNIX standard, BTW) the source is always on the left, and the destination is always on the right.So let's load ebx with the value in eax:（AT&T的目的在右，Intel的目的在左）

	- AT&T:  movl %eax, %ebx
	- Intel: mov ebx, eax

##### Constant value/immediate value format:

You must prefix all constant/immediate values with "$".
Let's load eax with the address of the "C" variable booga, which is static.

	- AT&T:  movl $_booga, %eax
	- Intel: mov eax, _booga
Now let's load ebx with 0xd00d:

AT&T:  movl $0xd00d, %ebx

Intel: mov ebx, d00dh

##### Operator size specification:

You must suffix the instruction with one of b, w, or l to specify the width of the destination register as a byte, word or longword. If you omit this, GAS (GNU assembler) will attempt to guess. You don't want GAS to guess, and guess wrong! Don't forget it.

	-AT&T:  movw %ax, %bx
	-Intel: mov bx, ax

The equivalent forms for Intel is byte ptr, word ptr, and dword ptr, but that is for when you are...

##### Referencing memory:
DJGPP uses 386-protected mode, so you can forget all that real-mode addressing junk, including the restrictions on which register has what default segment, which registers can be base or index pointers. Now, we just get 6 general purpose registers. (7 if you use ebp, but be sure to restore it yourself or compile with -fomit-frame-pointer.)

Here is the canonical format for 32-bit addressing:

	AT&T:  immed32(basepointer,indexpointer,indexscale)
	Intel: [basepointer + indexpointer*indexscale + immed32]

You could think of the formula to calculate the address as:

  immed32 + basepointer + indexpointer * indexscale

You don't have to use all those fields, but you do have to have at least 1 of immed32, basepointer and you MUST add the size suffix to the operator!
Let's see some simple forms of memory addressing:

- Addressing a particular C variable:(定位一个C变量)
	
	AT&T:  _booga

	Intel: [_booga]

Note: the underscore ("_") is how you get at static (global) C variables from assembler. This only works with global variables. Otherwise, you can use extended asm to have variables preloaded into registers for you. I address that farther down.
- Addressing what a register points to:
	
	AT&T:  (%eax)

	Intel: [eax]

- Addressing a variable offset by a value in a register:

	AT&T: _variable(%eax)

	Intel: [eax + _variable]

- Addressing a value in an array of integers (scaling up by 4):

	AT&T:  _array(,%eax,4)

	Intel: [eax*4 + array]

- You can also do offsets with the immediate value:
C code: *(p+1) where p is a char *

	AT&T:  1(%eax) where eax has the value of p
	
	Intel: [eax + 1]

- You can do some simple math on the immediate value:

	AT&T: _struct_pointer+8

I assume you can do that with Intel format as well.


- Addressing a particular char in an array of 8-character records:

eax holds the number of the record desired. ebx has the wanted char's offset within the record.

	AT&T:  _array(%ebx,%eax,8)
	
	Intel: [ebx + eax*8 + _array]

Whew. Hopefully that covers all the addressing you'll need to do. As a note, you can put esp into the address, but only as the base register.

#Basic inline assembly

The format for basic inline assembly is very simple, and much like Borland's method.
    asm ("statements");
Pretty simple, no? So
	asm ("nop");
will do nothing of course, and
	asm ("cli");
will stop interrupts, with
	asm ("sti");
of course enabling them. You can use __asm__ instead of asm if the keyword asm conflicts with something in your program.
When it comes to simple stuff like this, basic inline assembly is fine. You can even push your registers onto the stack, use them, and put them back.
```
asm ("pushl %eax\n\t"
     "movl $0, %eax\n\t"
     "popl %eax");
```
(The \n's and \t's are there so the .s file that GCC generates and hands to GAS comes out right when you've got multiple statements per asm.)
It's really meant for issuing instructions for which there is no equivalent in C and don't touch the registers.
But if you do touch the registers, and don't fix things at the end of your asm statement, like so:
```
asm ("movl %eax, %ebx");
asm ("xorl %ebx, %edx");
asm ("movl $0, _booga");
```
then your program will probably blow things to hell. This is because GCC hasn't been told that your asm statement clobbered ebx and edx and booga, which it might have been keeping in a register, and might plan on using later. For that, you need:(然后你的程序可能会把事情搞得一团糟。 这是因为GCC没有被告知你的asm语句破坏了ebx和edx以及booga，它可能一直保存在寄存器中，并且可能计划稍后使用。 为此，您需要：)

#Extended inline assembly

The basic format of the inline assembly stays much the same, but now gets Watcom-like extensions to allow input arguments and output arguments.
Here is the basic format:

asm ( "statements" : output_registers : input_registers : clobbered_registers(意思是被破坏的寄存器));
Let's just jump straight to a nifty example, which I'll then explain:
```
asm ("cld\n\t"
     "rep\n\t"
     "stosl"
     : /* no output registers */
     : "c" (count), "a" (fill_value), "D" (dest)
     : "%ecx", "%edi" );
```
The above stores the value in fill_value count times to the pointer dest.
Let's look at this bit by bit.

	asm ("cld\n\t"

We are clearing the direction bit of the flags register. You never know what this is going to be left at, and it costs you all of 1 or 2 cycles.
     "rep\n\t"
     "stosl"
Notice that GAS requires the rep prefix to occupy a line of it's own. Notice also that stos has the l suffix to make it move longwords.
     : /* no output registers */
Well, there aren't any in this function.
     : "c" (count), "a" (fill_value), "D" (dest)
Here we load ecx with count, eax with fill_value, and edi with dest. Why make GCC do it instead of doing it ourselves? Because GCC, in its register allocating, might be able to arrange for, say, fill_value to already be in eax. If this is in a loop, it might be able to preserve eax thru the loop, and save a movl once per loop.
     : "%ecx", "%edi" );
And here's where we specify to GCC, "you can no longer count on the values you loaded into ecx or edi to be valid." This doesn't mean they will be reloaded for certain. This is the clobberlist.
Seem funky? Well, it really helps when optimizing, when GCC can know exactly what you're doing with the registers before and after. It folds your assembly code into the code it's generates (whose rules for generation look remarkably like the above) and then optimizes. It's even smart enough to know that if you tell it to put (x+1) in a register, then if you don't clobber it, and later C code refers to (x+1), and it was able to keep that register free, it will reuse the computation. Whew.

Here's the list of register loading codes that you'll be likely to use:

a        eax
b        ebx
c        ecx
d        edx
S        esi
D        edi
I        constant value (0 to 31)
q,r      dynamically allocated register (see below)
g        eax, ebx, ecx, edx or variable in memory
A        eax and edx combined into a 64-bit integer (use long longs)
Note that you can't directly refer to the byte registers (ah, al, etc.) or the word registers (ax, bx, etc.) when you're loading this way. Once you've got it in there, though, you can specify ax or whatever all you like.
The codes have to be in quotes, and the expressions to load in have to be in parentheses.

When you do the clobber list, you specify the registers as above with the %. If you write to a variable, you must include "memory" as one of The Clobbered. This is in case you wrote to a variable that GCC thought it had in a register. This is the same as clobbering all registers. While I've never run into a problem with it, you might also want to add "cc" as a clobber if you change the condition codes (the bits in the flags register the jnz, je, etc. operators look at.)

Now, that's all fine and good for loading specific registers. But what if you specify, say, ebx, and ecx, and GCC can't arrange for the values to be in those registers without having to stash the previous values. It's possible to let GCC pick the register(s). You do this:

asm ("leal (%1,%1,4), %0"
     : "=r" (x)
     : "0" (x) );
The above example multiplies x by 5 really quickly (1 cycle on the Pentium). Now, we could have specified, say eax. But unless we really need a specific register (like when using rep movsl or rep stosl, which are hardcoded to use ecx, edi, and esi), why not let GCC pick an available one? So when GCC generates the output code for GAS, %0 will be replaced by the register it picked.
And where did "q" and "r" come from? Well, "q" causes GCC to allocate from eax, ebx, ecx, and edx. "r" lets GCC also consider esi and edi. So make sure, if you use "r" that it would be possible to use esi or edi in that instruction. If not, use "q".

Now, you might wonder, how to determine how the %n tokens get allocated to the arguments. It's a straightforward first-come-first-served, left-to-right thing, mapping to the "q"'s and "r"'s. But if you want to reuse a register allocated with a "q" or "r", you use "0", "1", "2"... etc.

You don't need to put a GCC-allocated register on the clobberlist as GCC knows that you're messing with it.

Now for output registers.

asm ("leal (%1,%1,4), %0"
     : "=r" (x_times_5)
     : "r" (x) );
Note the use of = to specify an output register. You just have to do it that way. If you want 1 variable to stay in 1 register for both in and out, you have to respecify the register allocated to it on the way in with the "0" type codes as mentioned above.
asm ("leal (%0,%0,4), %0"
     : "=r" (x)
     : "0" (x) );
This also works, by the way:
asm ("leal (%%ebx,%%ebx,4), %%ebx"
     : "=b" (x)
     : "b" (x) );

2 things here:

Note that we don't have to put ebx on the clobberlist, GCC knows it goes into x. Therefore, since it can know the value of ebx, it isn't considered clobbered.

Notice that in extended asm, you must prefix registers with **%%** instead of just %. Why, you ask? Because as GCC parses along for %0's and %1's and so on, it would interpret %edx as a %e parameter, see that that's non-existent, and ignore it. Then it would bitch about finding a symbol named dx, which isn't valid because it's not prefixed with % and it's not the one you meant anyway.

Important note: If your assembly statement must execute where you put it, (i.e. must not be moved out of a loop as an optimization（不可因为优化而移出循环）), put the keyword volatile after asm and before the ()'s. To be ultra-careful, use
__asm__ __volatile__ (...whatever...);

However, I would like to point out that if your assembly's only purpose is to calculate the output registers, with no other side effects, you should leave off the volatile keyword so your statement will be processed into GCC's common subexpression elimination optimization.
#Some useful examples
/#define disable() __asm__ __volatile__ ("cli");

/#define enable() __asm__ __volatile__ ("sti");
Of course, libc has these defined too.
/#define times3(arg1, arg2) \
__asm__ ( \
  "leal (%0,%0,2),%0" \
  : "=r" (arg2) \
  : "0" (arg1) );

/#define times5(arg1, arg2) \
__asm__ ( \
  "leal (%0,%0,4),%0" \
  : "=r" (arg2) \
  : "0" (arg1) );

/#define times9(arg1, arg2) \
__asm__ ( \
  "leal (%0,%0,8),%0" \
  : "=r" (arg2) \
  : "0" (arg1) );
These multiply arg1 by 3, 5, or 9 and put them in arg2. You should be ok to do:
times5(x,x);
as well.
/#define rep_movsl(src, dest, numwords) \
__asm__ __volatile__ ( \
  "cld\n\t" \
  "rep\n\t" \
  "movsl" \
  : : "S" (src), "D" (dest), "c" (numwords) \
  : "%ecx", "%esi", "%edi" )
Helpful Hint: If you say memcpy() with a constant length parameter, GCC will inline it to a rep movsl like above. But if you need a variable length version that inlines and you're always moving dwords, there ya go.
/#define rep_stosl(value, dest, numwords) \
__asm__ __volatile__ ( \
  "cld\n\t" \
  "rep\n\t" \
  "stosl" \
  : : "a" (value), "D" (dest), "c" (numwords) \
  : "%ecx", "%edi" )
Same as above but for memset(), which doesn't get inlined no matter what (for now.)
/#define RDTSC(llptr) ({ \
__asm__ __volatile__ ( \
        ".byte 0x0f; .byte 0x31" \
        : "=A" (llptr) \
        : : "eax", "edx"); })
Reads the TimeStampCounter on the Pentium and puts the 64 bit result into llptr.




#汇编语法
- cld和std

与cld相对应的指令是std，二者均是用来操作方向标志位DF（Direction Flag）。cld使DF 复位，即是让DF=0，std使DF置位，即DF=1.这两个指令用于串操作指令中。通过执行cld或std指令可以控制方向标志DF，决定内存地址是增大（DF=0，向高地址增加）还是减小（DF=1，向地地址减小）。

- lea（与mov类似）作用是进行地址传送，但是相比与mov（进行内容的移动）速度更快

-cmpw 就是cmp的一个word形式，只是影响标志位
http://www.fermimn.gov.it/linux/quarta/x86/cmp.htm
- jne是一个条件转移指令。当ZF!=0，转至标号处执行。
- XOR <操作数1>,<操作数2>
－－－－其功能是将两个操作数进行异或运算，并将结果存放到操作数1中

-movsbl mov的拓展指令


ss 0x0000
sp 0x7000  
dx 0x1bcd

ax 0x8f
out al 0x70
in  0x71 al
in 0x92 al


### cr0寄存器

https://www.cnblogs.com/ck1020/p/6115200.html

### sub $0x2c,%esp
$0x2c-%esp之后放入esp中

###leave指令
在16位汇编下相当于:
mov bp,sp
pop bp
 
在32位汇编下相当于:
mov esp,ebp
pop ebp
###ret指令

###call指令

对于以上两个命令最简化的说法就是执行call的时候push prt
但是执行ret的时候就是pop IP这样就实现了函数的调用

稍微详细的解释
call：
CPU执行指令“call far ptr 标号”时，相当于进行：

push CS

push IP

jmp far ptr 标号

ret：
CPU执行ret指令时，相当于进行：

pop IP

 

CPU执行retf指令时，相当于进行：

pop IP

pop CS
详细的解释见：
http://www.cppblog.com/luqingfei/archive/2010/08/04/122170.aspx
 







