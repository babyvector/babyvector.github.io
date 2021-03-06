---
layout: post
title: 指针和引用
categories: [C/C++]
---
这是最常见的解释：

指针是一个**变量**，这个变量储存的是地址指向一个内存单元；引用是跟原来的变量实质上是一个东西，只不过是原变量的一个别名而已。
实例：
```
int a = 1; int *p = &a;
int a = 1; int &b = a;
```
上面定义了一个变量a和一个指针变量p，该指针变量指向a储存的位置，就是p储存的是a的地址；在第二行中定义了一个整型变量a和这个整型变量的引用b，实际上a和b是同一种东西，在内存中占据相同的位置。**也就是b只是a的一个别名**

同时在使用引用和指针的时候有以下几个注意事项：
1. **引用不可以为空，当被创建的时候，必须初始化，但是指针可以是空值**
2. 有const指针，但是没有const引用
3. **指针可以有多级，但是引用只能是一级**(int **p合法但是int &&a是不合法的)
4. 指针可以在初始化之后改变，即指向其他储存单元，但是引用在初始化后就不会再改变了
5. sizeof引用之后返回的是所指向变量的大小，但是sizeof指针返回的是储存指针变量的大小
6. 指针和引用的自增(++)运算意义不一样