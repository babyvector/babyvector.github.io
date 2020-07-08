---
layout: post
title: 递归
categories: [数据结构与算法]
tags: CBIR
---
### 代码部分（merge_pass由自己实现）


### 遍历递归

用于图的遍历

### 求解递归

比如最大子列和中的递归求法

比如求阶乘

比如二分查找

汉诺塔递归求解等

解法分析：将n个盘子从A座移动到C座可以分解为3个步骤：

* 将A上n-1个盘子借助C座先移动到B座上；
* 把A座上剩下的一个盘移动到C座上;
* 把n-1个盘从B座借助于A座移动到C座上;

3个步骤一共分为2类操作

* 将n-1个盘从一个座移动到另一个座上（n>1）.这就是大和尚让小和尚做的工作，它是一个递归的过程，即和尚将任务层层下放，直到第64个和尚为止。
* 将1个盘子从一个座上移动到另一个座上。这是大和尚自己做的工作。

{% highlight c++ %}
#include<stdio.h>
int main(){
	void hanoi(int n,char one,char two,char three);
	int m;
	printf("input the number of diskes:");
	scanf("%d",&m);
	hanoi(m,'A','B','C');
}
void hanoi(int n,char one,char two,char three){
	void move(char x,char y);
	if(n == 1){
		move(one,three);
	}else{
		hanoi(n-1,one,three,two);
		move(one,three);
		hanoi(n-1,two,one,three);
	}
}
void move(char x,char y){
	printf("%c->%c\n",x,y);
}
{% endhighlight %}


 

