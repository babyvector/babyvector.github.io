---
layout: post
title: 排序-归并排序
categories: [数据结构与算法]
tags: CBIR
mathjax: true
---
### 1.理论部分

#### 1.1自己的理论解释
   自己的理论理解就是：
   
   首先我们先要申请一个与要进行排序数列空间相同大小的空间，用来转移排序之后的数组

   对于一个数组而言（或者是链表）我们先对其中的相邻（间距为2）的两个元素进行排序

   然后将排序之后的数组返回

   对返回的数组再次进行一次间距为4（$2^2$）的排序排序之后的数组放入另一个数组并返回

   一直重复直到排序间距大于数组的长度则停止排序

$\sum_{i=0}^N\int_{a}^{b}g(t,i)\text{d}t$
#### 1.2图和复杂度表示

**不会**


|数据结构:	数组|

|最坏时间复杂度:	${\displaystyle \Theta (n\log n)} \Theta (n\log n)$|

|最优时间复杂度:	${\displaystyle \Theta (n)} \Theta(n)$|

|平均时间复杂度:	${\displaystyle \Theta (n\log n)} \Theta (n\log n)$|

|空间复杂度:	${\displaystyle \Theta (n)} \Theta(n)$|


#### 1.3复杂度分析



### 代码部分（merge_pass由自己实现）

{% highlight c++ %}
#include <stdio.h>

#define ElementType int
#define MAXN 100

void merge_pass(ElementType list[], ElementType sorted[], int N, int length);

void output(ElementType list[], int N)
{
	int i;
	for (i = 0; i<N; i++) printf("%d ", list[i]);
	printf("\n");
}

void  merge_sort(ElementType list[], int N)
{
	ElementType extra[MAXN];  /* the extra space required */
	int  length = 1;  /* current length of sublist being merged */
	while (length < N){
		merge_pass(list, extra, N, length); /* merge list into extra */
		output(extra, N);
		length *= 2;
		merge_pass(extra, list, N, length); /* merge extra back to list */
		output(list, N);
		length *= 2;
	}
}


int main()
{
	int N, i;
	ElementType A[MAXN];

	scanf("%d", &N);
	for (i = 0; i<N; i++) scanf("%d", &A[i]);
	merge_sort(A, N);
	output(A, N);

	return 0;
}

/* Your function will be put here */
//漏掉了以下的四点造成了很多错误，多花费了两个多小时
void merge_pass(ElementType list[], ElementType sorted[], int N, int length){
	int double_length = 2 * length;
	
	int start_pos = 0;
	int next_pos = start_pos + length;

	int sorted_sp = 0;

	int anchor1 = start_pos;
	int anchor2 = next_pos;
	while (N>anchor1+length){
		//（1）漏掉了N = start_pos情况 会出现这种情况 1 2 3 4 5 6 7 8 0 9  这种情况能混乱过关（加在了下面）
		//（2）最初写的时候是写成了start_pos+length,但是start_pos是一直会变化的。
		if( list[start_pos] < list[next_pos] ){
			sorted[sorted_sp++] = list[start_pos];
			if (start_pos+1 == anchor2){
				while (next_pos<N && next_pos<anchor2+length){
				//（3）next_pos<anchor2+length这一步没写造成无法将前段处理
					sorted[sorted_sp++] = list[next_pos++];
				}
				anchor1 = start_pos = anchor2 + length;
				anchor2 = next_pos = start_pos + length;//（4）这一步少写了
				
			}
			else{
				start_pos++;
			}
		}
		else{
			sorted[sorted_sp++] = list[next_pos];
			if (next_pos + 1 == N || next_pos + 1 == anchor2 + length){
				while (start_pos < anchor2){
					sorted[sorted_sp++] = list[start_pos++];
				}
				anchor1 = start_pos = anchor2 + length;
				anchor2 = next_pos = start_pos + length;
			}
			else{
				next_pos++;
			}

		}
	}
	
	if (N == anchor1+length){
		while (start_pos<N){
			sorted[sorted_sp++] = list[start_pos++];
		}
	}
	
}
{% endhighlight %}


### 维基百科上的相关代码

#### 代码1：迭代版

{% highlight c++ %}
template<typename T> //整數或浮點數皆可使用,若要使用物件(class)時必須設定"小於"(<)的運算子功能
void merge_sort(T arr[], int len) {
	T* a = arr;
	T* b = new T[len];
	for (int seg = 1; seg < len; seg += seg) {
		for (int start = 0; start < len; start += seg + seg) {
			int low = start, mid = min(start + seg, len), high = min(start + seg + seg, len);
			int k = low;
			int start1 = low, end1 = mid;
			int start2 = mid, end2 = high;
			while (start1 < end1 && start2 < end2)
				b[k++] = a[start1] < a[start2] ? a[start1++] : a[start2++];
			while (start1 < end1)
				b[k++] = a[start1++];
			while (start2 < end2)
				b[k++] = a[start2++];
		}
		T* temp = a;
		a = b;
		b = temp;
	}
	if (a != arr) {
		for (int i = 0; i < len; i++)
			b[i] = a[i];
		b = a;
	}
	delete[] b;
}
{% endhighlight %}


#### 代码2：递归版

{% highlight c++ %}
template<typename T>
void merge_sort_recursive(T arr[], T reg[], int start, int end) {
	if (start >= end)
		return;
	int len = end - start, mid = (len >> 1) + start;
	int start1 = start, end1 = mid;
	int start2 = mid + 1, end2 = end;
	merge_sort_recursive(arr, reg, start1, end1);
	merge_sort_recursive(arr, reg, start2, end2);
	int k = start;
	while (start1 <= end1 && start2 <= end2)
		reg[k++] = arr[start1] < arr[start2] ? arr[start1++] : arr[start2++];
	while (start1 <= end1)
		reg[k++] = arr[start1++];
	while (start2 <= end2)
		reg[k++] = arr[start2++];
	for (k = start; k <= end; k++)
		arr[k] = reg[k];
}
template<typename T> //整數或浮點數皆可使用,若要使用物件(class)時必須設定"小於"(<)的運算子功能
void merge_sort(T arr[], const int len) {
	T *reg = new T[len];
	merge_sort_recursive(arr, reg, 0, len - 1);
	delete[] reg;
}
{% endhighlight %}
