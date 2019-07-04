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

float 4            6位有效数字
double 8		   15位有效数字
long double 16	   19位有效数字

C++11中明确规定了每种变量的最少的占用的位数
*/
```

### 浮点类型的讲解

<center>[最好的讲解在这里！](https://www.cnblogs.com/findumars/p/5557203.html)</center>


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