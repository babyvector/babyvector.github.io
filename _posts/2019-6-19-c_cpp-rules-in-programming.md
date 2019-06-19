---
layout: post
title: 编程过程中的实用规则
categories: [C/C++]
---

* 关于变量命名
	* **常量全大写**
	* 类型名字开头字母要大写typedef struct{...}Pnode;
	* 变量全小写加_或者驼峰写法mightWork
* 关于内存申请malloc(),calloc(),type []
	* malloc()申请内存但是不进行初始化，申请堆中的内存，生命期是进程结束
		void *malloc(size_t size);
	* calloc()申请内存而且进行初始化,申请堆中的内存，生命期是进程结束
		void calloc(size_t num_elements,size_t size_of_element);
		上面一个函数相当于：

			p = malloc(num_elements*size_of_element);

			memset(p,0,num_elements*size_of_element);

		但是有的时候我们未必需要将申请的内存全部清零
	* type 申请内存但是是在栈上，出了函数就会被自动释放

* 关于为什么有些函数只能是成对出现
	* 比如viOpen()和viClose()只能是成对出现，因为这里面涉及到内存的申请和释放
* 关于结构体和typedef
	* 结构体属于复合类型，int属于基本类型，复合类型就是使用基本类型复合到一块，为了更加清晰的表达一个东西
	* typedef叫做类型定义，能够定义一个新的类型

	{%highlight ruby%}
		typedef struct _Node{
			...
		}Node;
	{%endhighlight%}
* 结构体中的内存对齐问题
	{%highlight ruby%}
	struct Node{
		int val;
		char a;
		Node * next;
	}
	{%endhighlight%}
	
将char a也放入一个四字节空间叫做4字节**对齐的放置**；如果不对齐的话叫做**紧缩放置**。但是做通讯协议的解析的时候也没办法的，因为通信的时候都是连续放置的。通常使用结构体来取也给通信协议的数据；但是一般进行通信协议解析的时候，人家定协议的人都是已经考虑过的这种情况

* 关于递归
	* 一般情况下谨慎使用递归，如果不是特殊情况(特别契合使用递归来做的 汉诺塔、leetcode726)尽量不使用递归，其实递归有时候相当于暴力穷举.而且基本上大部分递归都是可以配合堆栈使用循环来代替的。

* 关于函数封装
	* 能封装的函数一定要封装，这样才能够结构清晰

* 使用双向链表的时候可以直接使用头文件listhead.h文件
* 使用malloc一次性开辟大量的空间之后再进行单个的处理，这样能够节省大量的时间
	{%highlight ruby%}
		/*这种形式属于一次性开辟大量的空间，就像一个内存池*/
		PNode line_container = (PNode)malloc(Number_of_people * sizeof(Node));
		for (int i = 0; i < Number_of_people; i++) {
			line_container[i].value = i + 1;
		}

		/*这种就是属于单次进行申请，这样每次申请都要调用malloc()函数这样会耗费大量的时间*/
		PNode line_container = (PNode)malloc(sizeof(Node));
		line_container->value = 1;
		PNode line_container_head = line_container;
		for(int i = 1;i<Number_of_people;i++){
			line_container->next = (PNode)malloc(sizeof(Node));
			line_container->next->value = i;
			line_container = line_container->next;
		}
		line_container->next = NULL;
		line_container = line_container_head;
		/*
			第一种情况不仅只是调用了一次malloc()函数，而且在之后的运行过程中可以使用数组索引快速使用变量
		  	但是第二种情况是刚好相反的，多次调用malloc()函数而且申请之后的变量也是不连续的无法使用索引
		*/
	{%endhighlight%}
