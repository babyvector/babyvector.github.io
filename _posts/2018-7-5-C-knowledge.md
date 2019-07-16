---
layout: post
title: C语言知识复习
categories: C/C++语言
---


## 大一时期的C语言


## 记住大小写字母的ASCII的编码

	0 48
	A 65
	a 97

## 记住运算符的优先级

1. 内部运算符 () [] -> .
2. 单目运算符 
3. 算术运算符
4. 关系运算符
5. 位运算符
6. 逻辑运算符  大于等于的优先级要大于判断是否相等的优先级
7. 赋值运算符
8. 逗号运算符

## 大一时期的小程序

1.汉诺塔

```

	int main(){
		
		void hanoi(int n,char one,char two,char three);
		int m;
		printf("input the number of diskes:");
		scanf("%d",&m);
		printf("The step to move %d diskes:\n",m);
		hanoi(m,'A','B','C');
	
	
		return 0;
	}
	void move(char x,char y){
		printf("%c->%c\n",x,y);
	}
	void hanoi(int n,char one,char two,char three){
		
		//将n-1个盘子挪动到two上，再将最后一个盘子从one移动到three，最后再将two上的n-1个盘子移动到three上去就行了 
		if (n == 1){
			move(one,three);
			return;
		}
		else{
			hanoi(n - 1, one, three, two);
			move(one, three);
			hanoi(n - 1, two, one, three);
		}
		
	}

```

2.冒泡排序

```

	int main(){
		
		int  container[10] = {10,9,8,7,6,5,4,3,2,1};
		int N = 10;
		for (int j = N-1; j >= 1;j--){
			for (int i = 0; i < j; i++){
	
				if (container[i]<container[i + 1]){
					continue;
				}
				else{
					int temp = container[i];
					container[i] = container[i + 1];
					container[i + 1] = temp;
				}
			}
		}
		
		for (int i = 0; i < N;i++){
			printf("%d\n",container[i]);
		}
		return 0;
	}

```

3.求闰年

```

	int main(){
		int is_leap = -1;//初始化为即不是闰年也不是平常的年份，设这个值为1的时候就是闰年
		int year = -1;
		scanf("%d",&year);
		
		if (year%4 == 0 && year % 100 || year%400 == 0){
			is_leap = 1;
		}
		else{
			is_leap = 0;
		}
	
		if (is_leap){
			printf("the year is.\n");
		}
		else{
			printf("the year is not.\n");
		}
	
		return 0;
	}

```

4.常数转化为2进制

```

	#include<math.h>
	int main(){
		
		int n;
		scanf("%d",&n);
		int y_num = -1;
		int y_c = -1;
		while (n != 0){
			printf("%d",n%2);
			n /= 2;
		}
		printf("\n");
	
	
		return 0;
	}

```

5.求最大公约数

```

	int gcd(int a,int b){
		while (b != 0){
			int temp = b;
			b = a%b;
			a = temp;
	
		}
		return a;
	}
	int main(){
		
		int a, b;
		scanf("%d %d",&a,&b);
		int c = -1;
		c = gcd(a,b);
		printf("%d\n",c);
	
		return 0;
	}

```
6.求前n项的平方和
7.判断是否是素数

## 字符串处理函数

```

		#include<string.h>
		1.puts(char *);//将字符串输出到显示屏上
		2.get(char * string_container);//从显示屏上读入一个字符串
		3.char * strcat(char *s1,char *s2);//将两个字符串合并之后返回
		4.strcpy(char *str1,char *str2);//将字符串str2拷贝到str1上
		5.strncpy(str1,str2,n);
		6.int strcmp(char *str1,char *str2);//按照字典排序进行比较，如果str1>str2就返回一个正数其余类推
		7.strlen(char *str);//查不包括\0在内的字符串的长度
		8.strlwr(str);
		9.strupr(str);
```

## 变量占据空间的大小


```

		/*
			int 2/4  +-2^15
			uint
			short 2
			long 4
			ulong 4
			long long 8
			ulonglong 8
			
			float 4（字节）      1位      6位有效数字
			double 8（字节）		   15位有效数字
			long double 16（字节）	   19位有效数字
			
			C++11中明确规定了每种变量的最少的占用的位数
		*/
```

* 浮点型数据

例如：**5.5，其二进制数为101.1**，则用**二进制的科学计数法可以表示为 1.011 * 2^2**，这样小数点的位置就是固定的。

任何一个浮点数可以表示为下面的形式（国际标准IEEE 754）：

1. **(-1)^S * M * 2^E**

2. (-1)^S表示符号位，当S=0时，为正；当S=1时，为负

3. M表示有效数字，M>=1且M<2

4. 2^E表示指数位

上面的例子中，S=0，M=1.011，E=2。 
所以当存储浮点数时，只需要将上述三个数字保存起来，就可以了。 
对于但精度浮点数，其存储模型为：

![图在这里](https://img-blog.csdn.net/20180724144456190?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb2Rlc2h1c2h1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/2018072414585611?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb2Rlc2h1c2h1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)


所以二进制存储中只要储存起来**S、M、E**三段即可

* S段

	其中，S只可能为0或者1，所以只需用一个bit位即可保存。
* M段

	前面说过，**M>=1且M<2**，也就是M可以表示为1.xxxxxxxxxxx的形式，其中xxxxxxxxxxx即为小数部分。、 
	所以在存储M的时候，只需要存储后面的小数部分就可以了，比如上述的1.011，在存储的时候只存储011。等到读取的时候，只需在前面加上1即可。并且这样做，还可以节省一位有效数字。假如把1保存进去，则小数点后面只能保存22位有效数字，如果不把1保存进去，皆可以保存23位有效数字。
* E段

	但对于E而言，情况比较复杂：
首先，E是一个无符号整数（unsigned int） 
这表示，对于单精度浮点数而言，E的取值范围为0到255；对于双精度浮点数而言，E的取值范围为0到2047 
但是，在科学计数法中，E是可以取负数的 

	IEEE 754规定：存入内存时，E的真实值必须加上一个中间数 

	对于单精度浮点数，中间数为127 

	对于双精度浮点数，中间数为1023 

	列入，2^2在保存时，必须保存为2+127，即10000001

**E可以分为三种情况：**

**E不全为0或不全为1**

这种情况是比较常规的，按照上面的规则就可以了 
例如 5.5，则其二进制形式为：

0 10000010 01100000000000000000000

**E全为0**

此时，浮点数的指数E等于1-127（或1-1023）即为真实值 
有效数字M不再加上第一位的1，而是还原成0.xxxxxxxx的形式。 
此时是一个接近于0的很小的数字

**E全为1**

此时，如果有效数字M全为0，表示+无穷或-无穷（正负取决于S）。

>原文：https://blog.csdn.net/aixiaodeshushu/article/details/81186397 

### 常见的浮点类型

* float类型
	
	4字节共32位，其中1位正负位（0表示正数），8位标识小数点的位置，23位表示有效数字
	2^23=8,388,608	所以有效数字是6位

	S段1位
	
	E段8位

	M段23位

* double类型

	8字节共64位，其中1位正负位（0表示正数），11位表示小数点的位置，52位表示有效数字
	2^52=4,503,599,627,370,496,所以有效数字位为15位

	S段1位
	
	E段11位

	M段52位

* long double类型

	16字节共128位，其中1位正负位（0表示正数），15位表示小数位置，112位表示有效数字
	2^112=5.1922968585348276285304963292201e+33‬,官网上说有19位有效数字。
	
	S段1位
	
	E段15位

	M段112位

### 浮点类型的讲解

<center>[好的讲解在这里！](https://blog.csdn.net/aixiaodeshushu/article/details/81186397)</center>


## 计算机中的经验积累

* 计算机的编码方式


* 计算机中的参数

计算机中的形参和实参，**实际**存在的参数叫做实参，否则叫做形参

* 函数的定义

函数类型  函数名（参数类型1，参数名1，.......）

* 数组的初始化

```
	申请内存并进行初始化的时候
	int container[500] = {0};
	//这样的初始化是正确的，里面的所
	int container[500] = {-1};
	//这样的初始化是错误的只有第一个数字被初始化成为-1其余的全部被初始化为0

	测试代码：
		#include <stdio.h>
		#include <stdlib.h>
		
		int main()
		{
		    int array_not_init[10];
		    printf("print the not init one.\n");
		    for(int i = 0;i<10;i++){
		        printf("%d\n",array_not_init[i]);
		    }
		    int array_init[10] = {-1};
		    printf("print the init one.\n");
		    for(int i = 0;i<10;i++){
		        printf("%d\n",array_init[i]);
		    }
		    int *created_array = (int*)malloc(10*sizeof(int));
		    printf("print the malloced one.\n");
		    for(int i = 0;i<10;i++){
		        printf("%d\n",created_array[i]);
		    }
		    return 0;
		}

	测试结果：

		1966698477
		4200848
		6422352
		4200955
		print the init one.
		-1
		0
		0
		0
		0
		0
		0
		0
		0
		0
		print the malloced one.
		13241968
		13238464
		67108868
		34988
		13241968
		13238464
		13242360
		1398230852
		0
		0

```
```
|||test code.

int main(){
//	int a = 0;

//	(a++)++;
	//********************//要改动自己网站上的错误因为a++就已经不是左值了


	float b;
	int a = 2;
	b = 1.0 /(float) a;
	printf("%f\n",b);

	int c = 1, d = 0;
	printf("%d",c--||d);
	//continue 是提前结束本次循环
	return 0;
}
```



* 另外注意在for循环中的++号的使用

```
	for (position_to_insert,increments = 0; increments<porper_number;//这一步成立就行，自己使用break_if_repeat特别容易犯错
				increments++){
			position_to_insert = (input_num_container[i] + increments*increments) % porper_number;
			/*
			以上for语句与下面的for语句不同：
			for (position_to_insert,increments = 0; increments<porper_number;//这一步成立就行，自己使用break_if_repeat特别容易犯错
					position_to_insert = (input_num_container[i] + increments*increments++) % porper_number;){
			这样执行下来一定会比上的少一步，因为得到position之后increments就+1；
			而上面的的是先+1之后再得到position
				
			*/
			
			printf("position_to_insert:%d\n",position_to_insert);
		
			if (!is_be_filled[position_to_insert]){

			
				//result[i] = position_to_insert;
				is_be_filled[position_to_insert] = 1;
				p = position_to_insert;

				break;
			}
		}
```


## 关于sprintf()

{%highlight ruby%}

	在c语言中经常使用sprintf来进行数字到字符串的转化，注意

	char s[50];
	int x = 121;
	for(int i = 0;i<50;i++){
		s[i] = -1;
	}
	sprintf(s,"%d",x);//在sprintf之后s中的所有内容都会被改变

{%endhighlight%}

## 宏定义中的'\#\#'用法

	作用：
	将两个Token连接成一个Token

	示例：
		示例1：一个简单的示例
	
		#define LINK_MULTIPLE(a,b,c,d) a##_##b##_##c##_##d
		typedef struct _record_typeLINK_MULTIPLE(name,company,position,salary);
		// 这里这个语句将展开为：
		/ typedef struct _record_type name_company_position_salary;
		
		示例2：jos操作系统中的一个示例，加工传入的地址成一个新地址
		
		#define ENV_PASTE3(x, y, z) x ## y ## z
		
		#define ENV_CREATE(x, type)                                     \
        do {                                                            \
                extern uint8_t ENV_PASTE3(_binary_obj_, x, _start)[];   \
                env_create(ENV_PASTE3(_binary_obj_, x, _start),         \
                           type);                                       \
        } while (0)

## memmove() memset() memcpy()

原型：**void \*memmove( void\* dest, const void\* src, size_t count )**;
	
头文件：<string.h>
	
功能：由src所指内存区域复制count个字节到dest所指内存区域。
	
相关函数：memset、memcpy

## 关于char *name[10];char name[3][10]的区别

* char *name[10] 
	
	char \*name[10]是一个字符串指针**数组**，name这个变量存了10个字符串的指针;

	char *string_pointer[STRING_NUM] = {"hello","what is","yourname."};
	
	上面一行代码实际上是在程序执行之前在程序的.rodata段（**只读数据段**）生成了几个字符串，并将地址传入到了name变量;所以作为其形参类型可以是：char \*\*直接传入调用函数

* char name[3][10]
	
	char name[5][10]是一个是一个二维字符串数组

	char name[3][10] = {"my name","is","yaoming"};

	char name[3][10]是一个二维字符串数组，由于已经初始化了，所以放在程序的.bss段（Block Started by Symbol）属于静态内容分配；所以其形参类型必须是char[][10],或者char[3][10]

* 测试的程序

{%highlight ruby%}

		#include <stdio.h>
		#include <stdlib.h>
		
		#define STRING_NUM 3
		#define STRING_LENGTH 5
		
		char *string_pointer[STRING_NUM] = {"hello","what is","yourname."};
		
		char db_array[STRING_NUM][STRING_LENGTH] = {"my name","is","yaoming"};
		void print_s(char *s){
		    printf("%s\n",s);
		}
		
		void print_string_pointer(char **input_string_pointer){
		    for(int i = 0;i<STRING_NUM;i++){
		        print_s(input_string_pointer[i]);
		    }
		}
		void print_db_array(char input_db_array[][STRING_LENGTH]){
		    for(int i = 0;i<STRING_NUM;i++){
		        print_s(input_db_array[i]);
		    }
		}
		void gen_print(char ** in){
		    for(int i = 0;i<STRING_NUM;i++){
		        print_s(in[i]);
		    }
		}
		int main()
		{
		    putchar('\n');
		    print_string_pointer(string_pointer);
		    putchar('\n');
		    print_db_array(db_array);
		
		    putchar('\n');
		    gen_print(string_pointer);
		    putchar('\n');
		    gen_print(db_array);
		    return 0;
		}

{%endhighlight%}

## 关于程序编译后生成的各种段

* .bss

	用于存放未经过初始化的全局变量，或者初始化为0的全局变量
	
* .data

	用于存放经过初始化的全局变量

* .rodata

	常量区，用于存放只读常量，例如字符串之类

* .text

	正文段，作为程序运行的指令

* .stack
、
	由系统负责创建和释放，用于存放函数中的局部变量等，是函数能够运行的最基本的结构

* .heap

	使用malloc()等内存申请函数申请的空间，操作系统以4KB为单位给用户程序空间，由用户自己负责释放；或者等进程被操作系统杀死之后，由操作系统来回收。

## 在头文件最好不要进行变量定义

* 程序文件结构

		files---->main.c
			  |-->jos.c
              |-->jos.h

* main.c

		#include<stdio.h>
		#include"jos.h"

		int main(){
			
						
			return 0;
		}
	
* jos.c

		#include"jos.h"
		void jos_print(char ** input){
		    return 0;
		}


* jos.h

		#ifndef _JOS_H_
		#define _JOS_H_
		char *name[10] = {"what","is yourname"};
		void jos_print(char ** input);
		
		
		#endif

* 编译会出现一个**链接**时错误

	原因：mingw32/bin/ld.exe: obj\Debug\main.o:main.c:(.data+0x0): multiple definition of `name'; obj\Debug\jos.o:jos.c:(.data+0x0): first defined here

	也就是变量出现了重定义，因为两个变量都有这个文件