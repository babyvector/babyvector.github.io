---
layout: post
title: jos-练习10
categories: [操作系统]
---
#start
//以下是自己的写的函数，但是参数无法断定因为参数个数是由编译器决定的
//但是通过这个函数的编写我们能够了解C语言在内存中是如何配合esp和ebp来使用堆栈的
//有助于我们理解函数的嵌套和调用机制在操作系统总的实现
int mon_backtrace(int argc, char **argv, struct Trapframe *tf)
{
        // Your code here.
        int esp_position = read_ebp();//read the ebp of the mon_b function which points to the positon of the ebp which belongs to the before function(nested in) and the position+1 is the ret ip value of this mon_b function and position+n is the n-1 pra that sent into this funcion.      
        int before_ebp = *(int*)esp_position;
        while(before_ebp != 0){//here the ebp is 0,because the i386_init set it 0 before it comes into a real function
                before_ebp = *(int*)esp_position;//read the ebp of the before stack
                int eip = (int)*((int*)esp_position+1);
                int pra_1 = (int)*((int*)esp_position+2);
                int pra_2 = (int)*((int*)esp_position+3);
                int pra_3 = (int)*((int*)esp_position+4);
                int pra_4 = (int)*((int*)esp_position+5);
                int pra_5 = (int)*((int*)esp_position+6);

                cprintf("ebp:0x%8.0x eip:0x%8.0x 0x%8.0x 0x%8.0x 0x%8.0x 0x%8.0x 0x%8.0x\n",esp_position,eip,pra_1,pra_2,pra_3,pra_4,pra_5);
        }

        return 0x1001;
}

其中我们函数使用栈空间的模型是进入函数之后
function(inx,...)
{
push   %ebp		//保存ebp
mov    %esp,%ebp//将esp推入到ebp中//以上两步是进入函数的必备
sub    $0xc,%esp//为局部变量留的空间

int a = mon_backtrace(1,0,0);
//当我们在函数中调用一另一个函数的时候
f0100060:       6a 00                   push   $0x0
f0100062:       6a 00                   push   $0x0
f0100064:       6a 01                   push   $0x1//将函数的三个变量推入栈中
f0100066:       e8 10 07 00 00          call   f010077b <mon_backtrace>


}//对应的指令如下
ret

//对应的栈中的空间存储的内容如下Stack地址由上到下，地址依次减小

传入这个函数的参数1

传入这个函数的参数2

传入这个函数的参数...   ->以上字节数由编译器所决定

return address pointer  ->4字节 当我们进入的函数ret的时候会弹出这个地址并返回到这里，刚好也就是执行下一步。

old ebp	->4字节

//之后old ebp位置的地址esp被放入当前函数的ebp中，这样我们才能实现读ebp内容就能回溯函数


>https://blog.csdn.net/skyuppour/article/details/44892047

>https://www.cnblogs.com/fatsheep9146/p/5070145.html