---
layout: post
title: jos-练习9
categories: [操作系统]
---


//这是kernel.asm中的内容，其中#define test_value与函数mon_test都是自己为了搞清汇编函数的运行过程而自己编写的函数
f010005a <test_backtrace>:
#define test_value 100
void
test_backtrace(int x)
{
f010005a:       55                      push   %ebp
f010005b:       89 e5                   mov    %esp,%ebp//以上两步是进入函数的必备
f010005d:       83 ec 0c                sub    $0xc,%esp//为局部变量留的空间
        int y = 0;
        int z = 0;
        int a = mon_backtrace(1,0,0);
f0100060:       6a 00                   push   $0x0
f0100062:       6a 00                   push   $0x0
f0100064:       6a 01                   push   $0x1//将函数的三个变量推入栈中
f0100066:       e8 10 07 00 00          call   f010077b <mon_backtrace>
 int b = mon_test(2,0,4,5,6,7,0);
f010006b:       83 c4 0c                add    $0xc,%esp//复用之前函数使用的空间
f010006e:       6a 00                   push   $0x0
f0100070:       6a 07                   push   $0x7
f0100072:       6a 06                   push   $0x6
f0100074:       6a 05                   push   $0x5
f0100076:       6a 04                   push   $0x4
f0100078:       6a 00                   push   $0x0
f010007a:       6a 02                   push   $0x2
f010007c:       e8 15 07 00 00          call   f0100796 <mon_test>
        int c = mon_test(2,0,4,5,6,7,0);
f0100081:       83 c4 1c                add    $0x1c,%esp//为了复用之前函数的使用空间
f0100084:       6a 00                   push   $0x0
f0100086:       6a 07                   push   $0x7
f0100088:       6a 06                   push   $0x6
f010008a:       6a 05                   push   $0x5
f010008c:       6a 04                   push   $0x4
f010008e:       6a 00                   push   $0x0
f0100090:       6a 02                   push   $0x2
f0100092:       e8 ff 06 00 00          call   f0100796 <mon_test>
//      mon_backtrace(0,0,0);
//      just_test(0,0,0,'a');
}
f0100097:       83 c4 20                add    $0x20,%esp//为了让之后的函数能够复用之前使用的空间
f010009a:       c9                      leave
f010009b:       c3                      ret//使用sp与bp返回到调用这个函数的函数



//如果我们想要搞清楚更多函数调用的堆栈的使用看以下的link
https://blog.csdn.net/skyuppour/article/details/44892047
//关于leave和ret和call以及如何获得IP值的见exercise1





