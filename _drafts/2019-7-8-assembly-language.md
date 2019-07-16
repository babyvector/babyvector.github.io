---
layout: post
title: 汇编
categories: [C/C++]
---

学习来源：
> https://pdos.csail.mit.edu/6.828/2018/readings/pcasm-book.pdf

已经下载的来源：
>E:\xyk\Github\babyvector.github.io\pdf\pcasm-book.pdf

>https://babyvector.github.io/pdf/yongkangxu.pdf

# chapter 1 Introduction

## 1.1 数字系统

### 十进制

$234 = 2*10^2+3*10^1+4*10^0$

### 二进制

$11001(2进制)=1*2^4+1*2^3+0*2^2+0*2^1+1*2^0$
$=16+8+1$
$=25$

### 16进制

$2BD(16进制)=2*16^2+11*16^1+13*16^0$
$=512+176+13$
$=701$

## 1.2 计算机组成

### 1.2.1 内存

计算机中最基本的存储单元是字节。**计算机中的每个字节都是可以被寻址的。**所有的信息都是被数字化的，其中字符串是按照ASCII进行编码存储的。

### 1.2.2 CPU

CPU也叫做中央存储单元，是执行指令的物理装置。CPU执行的指令非常的简单，指令可能要求它们所作用的数据被存储在一个特殊的储存装置叫做寄存器。CPU读取寄存器中的数据的速度要远大于读取内存中数据的速度。然而，CPU中的寄存器的数量是有限的，所以编程人员应该注意将使用的数据放置到寄存器中。**每个构架的CPU，都有自己的机器语言，所以也对应这不同类型的编程语言**

### 1.2.3 80x86 CPU 家族

这个CPU家族有许多相同的特征，包括基本的机器语言。但是**这个家族最近的新成员已经极大的增强了某些特性**。

**8088，8086：**它们两个是最早的用在PC机上的CPU，提供几个16位的寄存器:**AX,BX,CX,DX,SI,DI,BP,SP,CS,DS,SS,ES,IP,FLAGS**只支持1MB的内存，而且只能运行在实模式。在这个模式下，一个程序能够访问任何地址的内存，**甚至是其他程序的占用的地址空间**。这使得调试和程序运行安全变得非常困难。同样，程序必须分段，而且每个段的大小不能大于64KB。

**80286：**这个CPU是被用在AT系列的PC机上。它相较于8088/86增加了一些新的指令。当然它的**最主要特征就是16位的保护模式。**在这个模式下，它能访问最高16MB的内存，同时保护其他程序运行的地址空间。然而，程序仍然需要分段，而且每段的大小不能超过64KB。

**80386：**这个CPU就是80286的增强版。首先他将很多寄存器由16位拓展至32位**（EAX,EBX,ECX,EDX,ESI,EDI,EBP,ESP,EIP）**而（CS,DS,SS,ES）并没有发生变化。同时增加了**两个新的16位的寄存器FS和GS**,同时还引入了32位的保护模式。在这个模式下，它能寻址最高4GB，程序仍然需要进行分段，但是每个段最高可以分配到4GB。

**80486/Pentium/Pentium Pro:**这些成员相对于8086家族的CPU只增加了很少的新特性。主要的改变是加速了时钟频率从而加速了执行效率。

**Pentium MMX:**这个处理器增加了MMX（MultiMedia eXentions）指令。这些指令可以加速通用的图形操作。

**Pentium II:**这是一个加入了MMX指令的Pentium Pro处理器。（Pentium III只是II的频率提升版本）


		AX寄存器：（AH|AL）

### 1.2.4 8086的16位寄存器

在8086的16位寄存器中可以分为高8位AH和低8位AL.更改AX寄存器的值就会改变AH和AL中的值。AH和AL是两个独立的一个字节的寄存器。**注意：当我们使用AX的时候，这两个寄存器就不是独立的寄存器，更改AX会同时更改AL和AX。**通用的寄存器通常被用在移动数据和进行算术运算的指令中。

CPU内有**两个索引寄存器:SI和DI**。它们经常被用作指针，但是也可以用作通常的通用寄存器，但是不可以像AX那样拆成：AH和AL两个8位的寄存器使用。

16位寄存器**BP和SP寄存器**通常用于指向机器中的数据语言**堆栈**。这些将在后面讨论。

16位**CS,DS,SS和ES寄存器**是段寄存器。它们表示用于程序的不同部分的内存。CS表示代码段。DS表示数据段。SS表示堆栈段。ES表示额外段。ES一般用作临时的段寄存器。相关寄存器的细节再Sections 1.2.6和1.2.7.

指令指针（IP）寄存器和CS寄存器一起使用跟踪CPU要执行的下一条指令的地址。通常，当执行指令时，IP被提前指向内存中的下一条指令。

**FLAGS寄存器保存前一条指令执行结果的重要信息。**这些结果是被保存在寄存器中的相应的对立的位中。例如，当Z位被置为1的时候，表示前一条指令执行的结果是0，如果不是0则指令执行结果不是0.**不是所有的指令都会修改FLAGS寄存器中的位，**请参阅附录中的表格，了解个别说明的效果FLAGS寄存器。

### 1.2.5 80386-32位寄存器

EAX表示引用32位寄存器AX是低16位，AH,AL还是表示AX中的高8位和低8位。我们没有办法去存取EAX的高16位。**段寄存器仍然是16位。**这个CPU也增加了两个新的寄存器FS和GS但是它们的名字并不代表任何东西。都是额外的临时段寄存器（就像ES一样）

术语**字的定义**之一是指数据寄存器的大小
的CPU。 对于80x86家族来说，这个词现在有点令人困惑。 在
表1.2中，人们看到该字被定义为2个字节（或16位）。 它是
在8086首次发布时给出了这个含义。 当80386是
发展起来后，决定将字的定义保持不变
虽然寄存器大小改变了。

### 1.2.6 实模式

在实模式的情况下，内存被限制到只有1MB（2^20），有效的地址实从0x00000到0xFFFFF.这些地址需要20位的数来表示。显然8086中的任何一个16位寄存器都无法表示这个地址。Intel使用两个16位的值通过运算后来表示这个值。第一个16位的值叫做selector（段选择子）。Selector必须被放入到段寄存器中。第二个16位的值叫做offset（偏移量）.真正的物理地址被一对16位的（selector:offset）通过公式计算出来。

		16*selector+offset

		(注意16*64KB=1024KB=1MB)

而表示为16进制的数乘以16是非常容易的，只要在数字的最右末尾加上一个0就行了。**实际上，selector选择子的值是一个段的编号。**

**实模式寻址的劣势：**

* 一个简单的选择子只能索引64K的内存（16位偏移量的上限）。**如果一个程序的代码大小超过64K呢？**CS中单个值不能被用于整个程序的执行过程。因此程序必须被分为很多部分（叫做段）在大小上小于64KB.当指令执行过程中从一个段到另一个段的过程中，CS寄存器中的值必须被改变。同样的问题同样发生在拥有大量数据的数据段（DS register）.这一点非常的令人苦恼。
* **内存中的每个字节没有特别的段地址！！**。比如，物理地址04808可以被解释为047C:0048，047D:0038,047E:0028或者047B:0058，这会使段地址的理解更加困难。

### 1.2.7 16位-保护模式

**<p style="color:red">
从实模式转变到保护模式的最大区别就是：！！段选择子变成了描述符表的索引，而不是真正的表示地址中的一段！！
</p>**

在80286的16位保护模式下，选择子的值(selector value)的含义与16位实模式是**完全不同**。在实模式下，一个选择子的值是一个描述符的索引。在所有的模式下程序被分为不同的段，**在实模式下，这些段位于固定位置的物理内存中，选择器值表示段落编号段的开头。**<p style="color:red">在保护模式下，段不是在物理内存中的固定位置。事实上它们有可能就不在内存中</p>。

保护模式使用了一种叫做**虚拟内存**的技术。基本思想就是虚拟内存系统只是将程序目前正在使用的数据和代码保存在内存中。其他数据和代码暂时存储在磁盘上，直到再次需要它们为止。在16位保护模式下，根据需要在内存和磁盘之间移动段。所有这些都是由操作系统透明地完成的。编写程序的人员不用考虑这些问题。

在保护模式下，每个段都在描述符表中有一个入口，这个入口有系统需要知道的关于这个段的所有信息。这些信息包括：段是否现在在内存中，是否有读取的权限（e.g.read-only）。这个入口段的入口的索引是段选择子的值，被存放在段寄存器中。(The index
of the entry of the segment is the selector value that is stored in segment
registers)16位保护模式的一大缺点是偏移仍然是16位量。 因此，分段大小仍然限制在最多64K。 这使得大型阵列的使用成为问题。

### 1.2.8 32位保护模式

80386中引入了32位保护模式。386的32位保护模式和286的16位的保护模式主要有两个区别：

1. 偏移量拓展到了32位。这样允许偏移量最多寻址4GB的地址空间。因此段的大小提升到了4GB.
2. 段可以被划分为更小的4K大小单元的页。**现在虚拟内存使用分页技术，而不再使用分段技术**。这意味着在任意时间可能只有段的一部分或者全部不在内存中。而286的16位保护模式，要么整个段在内存中要么全部不再，这对于允许使用更大段的32位的保护模式是不实用的。

Windows9X,Windows NT/2000/XP,OS/2,Linux都是以分页的形式运行在32位保护模式下。

### 1.2.9 中断

有些时候必须中断程序的普通流程才能处理及时响应的事件。计算机硬件提供了一种叫做中断的机制去处理这些事件。例如，当一个鼠标移动了，鼠标硬件打断了正在执行的程序去处理鼠标移动事件（去移动显示器上的鼠标）。**中断导致控制传递给中断处理程序**。中断处理程序是处理中断的例程。 为每种类型的中断分配一个整数。 在物理存储器的开头，有一个中断向量表，其中包含中断的分段地址处理程序。 中断的数量本质上是该表的索引。

External interrupts are raised from outside the CPU. (The mouse is an example of this type.) Many I/O devices raise interrupts (e.g., keyboard,
timer, disk drives, CD-ROM and sound cards). Internal interrupts are raised from within the CPU, either from an error or the interrupt instruction. Error
interrupts are also called *traps*. Interrupts generated from the interrupt instruction are called *software interrupts*. DOS uses these types of interrupts to implement its API (Application Programming Interface). More modern operating systems (such as Windows and UNIX) use a C based interface.

大多数中断处理程序在处理完程序之后就会将控制权返回给被中断的程序。他会将所有的寄存器恢复到原来正运行的程序被中断时的状态。因此，中断程序能够像什么事情都没有发生一样去运行（除了丢失一些CPU cycles）。**而异常通常情况下不返回。它们通常会终止程序**。

## 1.3 汇编语言

### 1.3.1 机器语言

每种类型的CPU都有自己的机器语言。80x86结构的CPU中的机器码中有可能既含有操作码又含有操作数。并且非常难记。比如：

		EAX=EAX+EBX
		机器码为：
		03 C3


### 1.3.2 汇编语言

结构：助记符   操作数

写一个assembler比compiler简单的多，因为汇编中每一条只代表一条指令，而compiler编译的一句中甚至包含很多条语句，计算机科学家甚至花费了很多时间去想象如何编写一个compiler。

Linux中使用的assembler是NASM，Windows中使用的是MASM。它们二者之间有一些不同。

### 1.3.3  指令操作数

机器代码指令具有不同数量和类型的操作数; 但是，通常，每条指令本身都有固定数量的操作数（0到3）。 操作数可以具有以下类型：

* 寄存器：这些操作数直接指向CPU中寄存器的内容。
* 内存：这些是指内存中的数据。 数据的地址可以是硬编码到指令中的常数，或者可以使用寄存器的值来计算。 <p style="color:red">地址被认为是偏离段的开头。（所以讲:地址都是虚假的！）</p>。
* 立即数：*这些是指令本身中列出的固定值。它们存储在指令本身（在代码段中），并没有存放在数据段中。*
* 暗示：没有明确显示操作数。 例如，递增指令将1添加到寄存器或存储器。 这是隐含的。

### 1.3.4 基本指令

（mov,add,sub,inc,dec）

最基本指令：MOV 将一个地址的值拷贝到另外一个地址。

		MOV dest,src

注意：操作数占用的空间的大小必须相同，AX中的值就不能被存储在BL中。


**示例：**
			mov eax, 3 ; store 3 into EAX register (3 is immediate operand)
			mov bx, ax ; store the value of AX into the BX register
	The ADD instruction is used to add integers.
	
			add eax, 4 ; eax = eax + 4
			add al, ah ; al = al + ah
	The SUB instruction subtracts integers.
	
			sub bx, 10 ; bx = bx - 10
			sub ebx, edi ; ebx = ebx - edi
	The INC and DEC instructions increment or decrement values by one.
	Since the one is an implicit operand, the machine code for INC and DEC is
	smaller than for the equivalent ADD and SUB instructions.
	
			inc ecx ; ecx++
			dec dl ; dl--

### 1.3.5 指令

指令是汇编程序而非CPU的工件。 它们通常用于指示汇编程序执行某些操作或通知汇编程序某些内容。 它们不会被翻译成机器代码。 指令的常见用途是：

* 定义常量
* 定义内存以存储数据
* 将内存分成不同的段
* 有条件的包括源代码
* 包括其他文件


NASM代码像C一样通过预处理器。它有许多与C相同的预处理器命令。但是，NASM的预处理器指令以％而不是C中的#开头。

**equ指令**

equ指令可用于定义符号。 符号是命名常量，可以在汇编程序中使用。 格式为：

		symbol equ value

symbol的值不可以在后面被重新定义。

*表格1.3*

		Unit			Letter
		byte			B
		word			W
		double world	D
		quad world		Q
		ten bytes		T

**%define 指令**

这个指令与C语言中的#define非常相似。但是能够被重新赋值。


**数据指令：**

数据指令用于数据段以定义内存空间。
有两种方法可以保留内存。 第一种方式只定义数据空间; 第二种方式定义空间和初始值。 第一种方法使用RESX指令之一。 X被替换为一个字母，用于确定将要存储的对象（或多个对象）的大小。 表1.3显示了可能的值。
第二种方法（也定义了初始值）使用其中一种方法DX指令。 X字母与RESX指令中的字母相同。用标签标记内存位置是很常见的。 标签允许一个可以轻松引用代码中的内存位置。 以下是几个例子：

		L1 db 0 ; byte labeled L1 with initial value 0
		L2 dw 1000 ; word labeled L2 with initial value 1000
		L3 db 110101b ; byte initialized to binary 110101 (53 in decimal)
		L4 db 12h ; byte initialized to hex 12 (18 in decimal)
		L5 db 17o ; byte initialized to octal 17 (15 in decimal)
		L6 dd 1A92h ; double word initialized to hex 1A92
		L7 resb 1 ; 1 uninitialized byte
		L8 db "A" ; byte initialized to ASCII code for A (65)



双引号和单引号的处理方式相同。 连续的数据定义按顺序存储在内存中。 也就是说，单词L2紧接在存储器中的L1之后存储。 还可以定义存储器的序列

		L9 db 0, 1, 2, 3 ; defines 4 bytes
		L10 db "w", "o", "r", ’d’, 0 ; defines a C string = "word"
		L11 db ’word’, 0 ; same as L10

对于大的存储序列,NASM的**TIMES指令**非常有用

		L12 times 100 db 0 ; equivalent to 100 (db 0)’s
		L13 resw 100 ; reserves room for 100 words

请记住标签可以用来引用代码中的数据。标签一般有两种使用方式。如果只是使用普通标签，则将其解释为数据的地址（或者偏移量）。如果标签是在一对方括号中间，则其将被解释为在标签地址位置的内存中的数据。换句话说，应该将标签视为指向数据的指针，方括号取消引用指针，就像星号在C中所做的那样。在32位模式中，地址是32位的。以下是一些例子：

		mov al, [L1] ; copy byte at L1 into AL
		2 mov eax, L1 ; EAX = address of byte at L1
		3 mov [L1], ah ; copy AH into byte at L1
		4 mov eax, [L6] ; copy double word at L6 into EAX
		5 add eax, [L6] ; EAX = EAX + double word at L6
		6 add [L6], eax ; double word at L6 += EAX
		7 mov al, [L6] ; copy first byte of double word at L6 into AL
	
实例的第7行显示了NASM的重要特性。 汇编程序不跟踪标签引用的数据类型。 它取决于
程序员确保他（或她）正确使用标签。 后来将数据地址存储在寄存器中并使用寄存器是很常见的就像C中的指针变量一样。再次，没有检查指针是否正确使用。 这样，组装比C甚至更容易出错。

考虑下面的指令：

		mov [L6],1		;store a 1 at L6

这个语句会产生一个operation size not specified error.产生的原因是：汇编器不知道将1存储为一个byte,word还是double word。想要解决这个问题，应该加一个大小的声明。

		mov dword[L6],1			;store a 1 at L6

这就告诉了汇编器去存储一个大小为double word的1在L6开始的位置。其他的大小说明符为:**BYTE,WORD,QWORD,TWORD**

*Table 1.4 Assembly I/O Routinues*

		
		print_int prints out to the screen the value of the integer stored in EAX
		print_char prints out to the screen the character whose ASCII value stored in AL
		print_string prints out to the screen the contents of the string at the address stored in EAX. The string must be a Ctype string (i.e. null terminated).
		print_nl prints out to the screen a new line character.
		read_int reads an integer from the keyboard and stores it into the EAX register.
		read_char reads a single character from the keyboard and stores its ASCII code into the EAX register.


### 1.3.6	Input 和 Output

这里作者直接引入了自己写的一个汇编库

		%include "asm_io.inc"

学习汇编是为了学习操作系统服务，依赖的库暂时不学。

### 1.3.7  Debugging 

* dump_regs
* dump_mem
* dump_stack
* dump_math

源码下载：
>http://pacman128.github.io/pcasm/

## 1.4 创建第一个程序

今天，很少去创建一个完全独立的汇编程序。汇编只是被用在一个程序中的关键点。为什么会这样？因为使用高级编程语言去编程更加简单。同样的原因是汇编程序难以移植到其他平台。实际上，现在已经很少用到汇编了。

那么为什么我们今天要学习会变呢？

1. 有时候汇编写出的程序更加小，运行起来更加的快相比于编译器生成的代码。
2. 汇编允许直接去使用硬件资源，高级语言难以做到
3. 学习汇编有助于理解计算机是如何工作的
4. 帮助理解编译器和高级语言

后面两点即使你今后不编程，这两点对你也非常的有用。实际上作者并不是每天编程，但是能从汇编中学到很多东西。

{%highlight ruby%}
		;
		; file: first.asm
		; First assembly program. This program asks for two integers as
		; input and prints out their sum.
		;
		; To create executable:
		; Using djgpp:
		; nasm -f coff first.asm
		; gcc -o first first.o driver.c asm_io.o
		;
		; Using Linux and gcc:
		; nasm -f elf first.asm
		; gcc -o first first.o driver.c asm_io.o
		;
		; Using Borland C/C++
		; nasm -f obj first.asm
		; bcc32 first.obj driver.c asm_io.obj
		;
		; Using MS C/C++
		; nasm -f win32 first.asm
		; cl first.obj driver.c asm_io.obj
		;
		; Using Open Watcom
		; nasm -f obj first.asm
		; wcl386 first.obj driver.c asm_io.obj
		
		%include "asm_io.inc"
		;
		; initialized data is put in the .data segment
		;
		segment .data
		;
		; These labels refer to strings used for output
		;
		prompt1 db    "Enter a number: ", 0       ; don't forget nul terminator
		prompt2 db    "Enter another number: ", 0
		outmsg1 db    "You entered ", 0
		outmsg2 db    " and ", 0
		outmsg3 db    ", the sum of these is ", 0
		
		
		;
		; uninitialized data is put in the .bss segment
		;
		segment .bss
		;
		; These labels refer to double words used to store the inputs
		;
		input1  resd 1
		input2  resd 1
		
		 
		
		;
		; code is put in the .text segment
		;
		segment .text
		        global  asm_main
		asm_main:
		        enter   0,0               ; setup routine
		        pusha
		
		        mov     eax, prompt1      ; print out prompt
		        call    print_string
		
		        call    read_int          ; read integer
		        mov     [input1], eax     ; store into input1
		
		        mov     eax, prompt2      ; print out prompt
		        call    print_string
		
		        call    read_int          ; read integer
		        mov     [input2], eax     ; store into input2
		
		        mov     eax, [input1]     ; eax = dword at input1
		        add     eax, [input2]     ; eax += dword at input2
		        mov     ebx, eax          ; ebx = eax
		        dump_regs 1               ; dump out register values
		        dump_mem 2, outmsg1, 1    ; dump out memory
		;
		; next print out result message as series of steps
		;
		        mov     eax, outmsg1
		        call    print_string      ; print out first message
		        mov     eax, [input1]     
		        call    print_int         ; print out input1
		        mov     eax, outmsg2
		        call    print_string      ; print out second message
		        mov     eax, [input2]
		        call    print_int         ; print out input2
		        mov     eax, outmsg3
		        call    print_string      ; print out third message
		        mov     eax, ebx
		        call    print_int         ; print out sum (ebx)
		        call    print_nl          ; print new-line
		
		        popa
		        mov     eax, 0            ; return back to C
		        leave                     
		        ret
		
		

{%endhighlight%}


程序的第13行定义了指定的程序部分存储在数据段中的内存（名称为.data）。只
应在此段中定义初始化数据。在第17至21行，有几个字符串被声明。它们将与C库一起打印，因此必须以空字符（ASCII码0）终止。记得有一个0和'0'之间的巨大差异。未初始化的数据应在bss段中声明（名为.bss
在第26行）。这个段的名称来自早期的基于UNIX的汇编程序运算符，意思是“由符号启动的块”。这也有一个堆栈段,稍后将对此进行讨论。代码段在历史上被命名为.text。这是指示
摆放在。请注意，主例程（第38行）的代码标签有一个
下划线前缀。这是C调用约定的一部分。此约定指定C在编译代码时使用的规则。这是非常重要的在连接C和汇编时知道这个约定。稍后将介绍整个机制;但是，现在，人们只需要知道所有C符号（即函数和全局变量）都有下划线C编译器附加到它们的前缀。 （此规则专门针对DOS / Windows，Linux C编译器不会在C符号名称前添加任何内容。）
第37行的全局指令告诉汇编程序将_asm_main作为全局标签。**与C不同，标签默认具有内部范围。这意味着只有同一模块中的代码才能使用该标签。**全局指令给出指定的标签（或标签）外部范围。这种类型的标签可以由程序中的任何模块访问。_asm_io模块声明了print int，et.al。标签是全局的。这就是人们可以在中使用它们的原因first.asm模块。

### 1.4.2 编译器依赖


		










	
	


