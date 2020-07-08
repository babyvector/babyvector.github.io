---
layout: post
title: 队列-数组实现循环队列
categories: [数据结构与算法]
tags: CBIR
---

## 采用数组实现循环队列需要注意的地方

1. 头节点不存放数据（否则当head==rear的时候会出现表示矛盾表示空或者满？）
2. 及时更新head和rear的值否则会造成越栈
3. 要注意队列为空或者队列为满的两个标志

## 简单的代码实现

{% highlight c++ %}
int queue[MaxVertexNum + 1];
int head = 0;
int rear = 0;
int push(int queue[],int input_element){
	
	if ( (head + 1) % (MaxVertexNum + 1)  == rear){
		return -1;
	}
	else{
		queue[head = (++head) % (MaxVertexNum + 1)] = input_element;
	}	
}
int pop(int queue[]){
	if (head == rear){
		return -1;
	}
	else{
		return queue[rear = (++rear) % (MaxVertexNum + 1)];
	}
}
{% endhighlight %}