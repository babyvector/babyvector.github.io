---
layout: post
title: jos-exercise8
categories: [操作系统]
---
#start

// (unsigned) octal
                case 'o':

                        // Replace this with your code.
                        //my code here start
                        num = getuint(&ap,lflag);
                        base = 8;
                        //my code end
                        putch('X', putdat);
                        putch('X', putdat);
                        putch('X', putdat);
                        goto number;
                        break;


Be able to answer the following questions:

1. Explain the interface between printf.c and console.c. Specifically, what function does console.c export? How is this function used by printf.c?

//stdio.h中包含的内容
//lib/console.c
void cputchar(int c);
int getchar(void);
int iscons(int fd);

//printf.c
我们平时写代码的时候最经常使用的是代码

cprintf(const char *fmt,...)//这里涉及到可变参数函数的用法，包含文件stdarg.h

cprintf(const char *fmt ,...)调用vcprintf(fmt,ap);
其中我们要注意va_start(ap,fmt)和va_end(ap)的用法

之后vcprintf(fmt,ap);调用vprintfmt((void*)putch,&cnt,fmt,ap);
其中(void*)putch调用的是console.c中的cputchar(ch)。

Note:我们需要注意的是，
* (void*)putch是需要调用底层(console.c)中的显示函数的
* cnt返回这次打印的个数（由%号个数决定）
* fmt是输入的整个的用于进行格式声明的字符串
* ap是va_list类型所定义的指向参数的指针，而va_start宏初始化变量ap其中
这个宏的的第二个参数是第一个可变参数的前一个参数是一个固定参数。
{% highlight ruby %}
//这里涉及到C语言中的可变参数的函数的编程
//头文件stdarg.h
//其中有三个函数是核心
void va_start( va_list arg_ptr, prev_param );

type va_arg( va_list arg_ptr, type ); 

void va_end( va_list arg_ptr ); 



cprintf(const char *fmt, ...)
{
		//首先我们要先声明一个va_list 类型的变量
        va_list ap;
        int cnt;
		//初始化ap
        va_start(ap, fmt);
		//在vcprintf(fmt,ap);函数中不断掉调用type va_arg( va_list arg_ptr, type ); 来获取...中的参数其中type类型是由fmt函数中分析得到
        cnt = vcprintf(fmt, ap);
		//最终将ap初始化为0
        va_end(ap);
		//如果我们想要更详细的了解这个函数就去头文件中寻找宏定义，宏定义能详细解释函数工作的原理
        return cnt;
}

//以下是vcprintf(fmt,ap)函数的部分
void
vprintfmt(void (*putch)(int, void*), void *putdat, const char *fmt, va_list ap)
{
        register const char *p;
        register int ch, err;
        unsigned long long num;
        int base, lflag, width, precision, altflag;
        char padc;

        while (1) {
                while ((ch = *(unsigned char *) fmt++) != '%') {
                        if (ch == '\0')
                                return;
                        putch(ch, putdat);
                }

                // Process a %-escape sequence
                padc = ' ';
//...
//在这个函数中我们可以看到直到fmt字符串中出现了'\0'才算是最终输出完毕
{% endhighlight %}

2. Explain the following from console.c:

{% highlight ruby %}
		//如果当前的光标闪烁的位置大于整个屏幕的尺寸（80*25）
1      if (crt_pos >= CRT_SIZE) {
2              int i;
				//将第2行到第80行的内容移动到第1行到第79行
3              memmove(crt_buf, crt_buf + CRT_COLS, (CRT_SIZE - CRT_COLS) * sizeof(uint16_t))；
4              //将第80行的字符改为空格
			   for (i = CRT_SIZE - CRT_COLS; i < CRT_SIZE; i++)
5                      crt_buf[i] = 0x0700 | ' ';
6              //将光标返回的第80行的开头继续进行显示
			   crt_pos -= CRT_COLS;
7      }

//crt_buf即是显示缓存用于字符，早期的显示平只能来显示字符
{% endhighlight %}

3. For the following questions you might wish to consult the notes for Lecture 2. These notes cover GCC's calling convention on the x86.
Trace the execution of the following code step-by-step:

int x = 1, y = 3, z = 4;
cprintf("x %d, y %x, z %d\n", x, y, z);
In the call to cprintf(), to what does fmt point? To what does ap point?
List (in order of execution) each call to cons_putc, va_arg, and vcprintf. For cons_putc, list its argument as well. For va_arg, list what ap points to before and after the call. For vcprintf list the values of its two arguments.

{%highlight ruby%}
//将代码编入到monitor.c函数中之后进行编译得到如下的文件：
f0100804:       e8 71 01 00 00          call   f010097a <cprintf>
        int x = 1,y = 3,z = 4;
        cprintf("x %d,y %x,z %x\n",x,y,z);
f0100809:       6a 04                   push   $0x4
f010080b:       6a 03                   push   $0x3
f010080d:       6a 01                   push   $0x1
f010080f:       68 23 1c 10 f0          push   $0xf0101c23
f0100814:       e8 61 01 00 00          call   f010097a <cprintf>
f0100819:       83 c4 20                add    $0x20,%esp
{%endhighlight%}







	