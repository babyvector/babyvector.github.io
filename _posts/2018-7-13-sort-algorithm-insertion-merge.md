---
layout: post
title: 排序算法总结-插入和归并排序
categories: [数据结构与算法]
---


## 问题的具体描述见PAT7-13
这一次的插入排序和归并排序都是自己手动完成的，之前也遇到过考察归并排序，希望以后能够见到更好的算法
```

#include<stdio.h>
#include<stdlib.h>

int source[101];
int source_copy[101];
int des[101];

int insert_sort(int * source, int * des, int N){
	int go_next = 1;
	for (int i = 0; i < N - 1; ++i){
		int j = 0;
		int temp = source[j = i + 1];
		for (j = i + 1; j - 1 >= 0 && temp < source[j - 1]; source[j] = source[j - 1], j--){
			;
		}
		source[j] = temp;
		//每执行一步进行一次检查，一步的定义？
		if (go_next == 0){
			printf("insert_sort:\n");
			for (int j = 0; j<N; j++){

				printf("%d ", source[j]);

			}
			printf("\n");
			return 1;
		}
		go_next = 0;
		for (int k = 0; k < N;k++){
			if (source[k] != des[k]){
				go_next = 1;
				break;
			}
		}
	}
	return 0;
}
int inner_merge_sort(int *source, int *des, int N, int space_number){
	int *transfer1 = (int *)malloc(sizeof(int)*space_number);
	int *transfer2 = (int *)malloc(sizeof(int)*space_number);

	int start_pos1 = 0;
	int end_pos1 = 0 + space_number;
	int start_pos2 = end_pos1;
	int end_pos2 = start_pos2 + space_number;//这里错误的初始化start_pos1！！！完全错误
	for (start_pos1 = 0,
			end_pos1 = 0 + space_number, 
				start_pos2 = end_pos1, 
					end_pos2 = (start_pos2 + space_number >= N ? N : start_pos2 + space_number); 
		 start_pos2 < N; 
		 start_pos1 = end_pos2, 
			end_pos1 = start_pos1 + space_number, 
				start_pos2 = end_pos1, 
					end_pos2 = (start_pos2 + space_number >= N ? N : start_pos2 + space_number)){

		//创建临时的数组盛放裂开的数组
		int inner_i = 0;
		for (int i = start_pos1; i < end_pos1; i++){
			transfer1[inner_i++] = source[i];
		}
		inner_i = 0;
		for (int i = start_pos2; i < end_pos2; i++){
			transfer2[inner_i++] = source[i];
		}
		//将transfer中的数据一个一个放入到source中
		int position1 = 0;//错误这里初始化成为了start_pos1！！！完全错误
		int position2 = 0;
		int i = 0;
		for ( i = start_pos1;
			  i < end_pos2&&position1<(end_pos1 - start_pos1) && position2<(end_pos2 - start_pos2);
			  i++){
			//上面的end_pos1-start_pos1之前写成了(end_pos2-start_pos1)/2这就造成了错误
			//还有这里会出现错误，因为position1，和position2有可能加到了有效值范围之外
			if (transfer1[position1]>transfer2[position2]){
				source[i] = transfer2[position2++];
			}
			else{
				source[i] = transfer1[position1++];
			}
		}
		//自己出现错误之后另行补救
		if (position1<end_pos1-start_pos1){
			for (int j = i; j < end_pos2;j++){
				source[j] = transfer1[position1++];
			}
		}
		if (position2<end_pos2-start_pos2){
			for (int j = i; j < end_pos2;j++){
				source[j] = transfer2[position2++];
			}
		}
	}
	return 1;
}
int merge_sort(int *source, int* des, int N){
	//主义终止的条件
	int go_next = 1;
	for (int i = 1; i < N; i *= 2){
		inner_merge_sort(source, des, N, i);
		//一次inner_merge_sort之后就进行一次比对，看题上是这么要求的
		if (go_next == 0){
			printf("merge_sort:\n");
			for (int j = 0; j<N; j++){

				printf("%d ", source[j]);

			}
			printf("\n");
			return 1;
		}
		go_next = 0;
		for (int k = 0; k < N; k++){
			if (source[k] != des[k]){
				go_next = 1;
				break;
			}
		}
	}
	return 0;
}

int main(){

	int N = 0;
	int nc = 0;
	scanf("%d", &N);
	nc = N;
	for (int i = 0; i < nc; i++){
		int a = 0;
		scanf("%d", &a);
		source[i] = a;
		source_copy[i] = a;
	}
	for (int i = 0; i < nc; i++){
		int b = 0;
		scanf("%d", &b);
		des[i] = b;
	}

	int return_value = merge_sort(source,des,N);
	if (!return_value){
		insert_sort(source_copy, des, N);
	}
	
	return 0;
}

```