---
layout: post
title: 图|网络流问题|最大流问题
categories: [数据结构与算法]
---

## Edmonds–Karp algorithm

1. 算法复杂度：O(V*E^2) 

2. Dinic's algorithm includes additional techniques that reduce the running time to O(V^2*E).

3. 例子(来源:wiki)
给定一个有7个节点的网络，源点为A，汇点G，各个边的容量如下图：

|... &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp; &nbsp;&nbsp; |...|..&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp; &nbsp;&nbsp;|
| :-: | :-: |
|  | ![](..\image\graph\EK-algorithm.png)  ||

在图中的边上的f/c表示，当前边的现在流量是f，边的容量是c，同时设节点$u$到$v$残留容量是$\text{c}_f(u,v) = c(u,v)-f(u,v)$,总的容量是减去之前已经用过的流量。如果从u到v的网络流是**负值**，它将会影响残留容量。
4. 接下来是算法的过程




| Capacity&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp; &nbsp;&nbsp; &nbsp;&nbsp; &nbsp;&nbsp; &nbsp;&nbsp;| Path | Resulting network |
| :-: | :-: | :-: |
|\begin{aligned}&\min(c_{f}(A,D),c_{f}(D,E),c_{f}(E,G))\\\\=&\min(3-0,2-0,1-0)=\\\\=&\min(3,2,1)=1\\end{aligned}|$A,D,E,G$|![](..\image\graph\EK-algorithm1.png)                                          |
|\begin{aligned}&\min(c_{f}(A,D),c_{f}(D,F),c_{f}(F,G))\\\\=&\min(3-1,6-0,9-0)\\\\=&\\min(2,6,9)=2\\end{aligned}|$A,D,F,G$|![](..\image\graph\EK-algorithm2.png)                                |
|\begin{aligned}&\min(c_{f}(A,B),c_{f}(B,C),c_{f}(C,D),c_{f}(D,F),c_{f}(F,G))\\\\=&\min(3-0,4-0,1-0,6-2,9-2)\\\\=&\\min(3,4,1,4,7)=1\\end{aligned}|$A,B,C,D,F,G$|![](..\image\graph\EK-algorithm3.png)                                                                                  |
|\begin{aligned}&\min(c_{f}(A,B),c_{f}(B,C),c_{f}(C,E),c_{f}(E,D),c_{f}(D,F),c_{f}(F,G))\\\\=&\min(3-1,4-1,2-0,0-(-1),6-3,9-3)\\\\=&\\min(2,3,2,1,3,6)=1\end{aligned}|$A,B,C,E,D,F,G$|![](..\image\graph\EK-algorithm4.png)|

Notice how the length of the **augmenting path** found by the algorithm (in red) never decreases. **The paths found are the shortest possible.** The flow found is equal to the capacity across the **minimum cut** in the graph separating the source and the sink. There is only one minimal cut in this graph, partitioning the nodes into the sets  $\\{A,B,C,E\\}and  \\{D,F,G\\}$, with the capacity

$c(A,D)+c(C,D)+c(E,G)=3+1+1=5.$




{%highlight ruby%}

	#include<stdio.h>
#include<iostream>

using namespace std;

#define maxn 7

struct graph{

	int capacity;
	int current_flow;
	int is_forward_direction;
	//假设输入数据为正向，反向容量为0，不过为了BFS能够遍历出反向的路径
	//需要在遍历的时候设置容量，而在进行求解最大流的时候反向容量应该设置为0
	//而反向流量应该设置为正向流量的相反数。同时在计算参与容量的时候正反向流
	//应该进行统一求解。

};
struct graph container[7][7];

class myque{
public:
	myque(){
		this->container = new int[default_num];
	}
	myque(int num_of_container){
		container = new int[num_of_container];
		default_num = num_of_container;
	}
	int is_empty(){
		if (end == head){
			return 1;
		}
		else{
			return 0;
		}
	}
	bool push(int input_num){
		if (!this->is_full()){
			container[(++end) % default_num] = input_num;
			return true;
		}
		else{
			cout << "full." << endl;
		}
	}
	int pop(){
		if (!is_empty()){
			return container[(++head) % default_num];
		}
		else{
			cout << "empty." << endl;
			return -1;
		}
	}
	~myque(){
		delete container;
	}
private:
	bool is_full(){
		if ( (end+1)%default_num == head){
			return true;
		}
		else{
			return false;
		}
	}
	int end  = 0;
	int head = 0;
	int default_num = 100;
	int *container;
};
bool isnot_in_stack(int *stack,int input,int stack_position){
	for (int i = stack_position; i >= 0;i--){
		if (stack[i] == input)
			return 0;
	}
	return 1;
}
int main(){
	for (int i = 0; i < maxn;i++){
		for (int j = 0; j < maxn;j++){
			container[i][j].capacity = 0;
			container[i][j].current_flow = 0;

		}
	}
	int N = 0, NC = 0;
	int S = 0, D = 0;

	cin >> N;
	NC = 7;
	cin.ignore();
	while (N--){
		int a = 0, b = 0, c = 0;
		cin >> a >> b >> c;
		cin.ignore();
		container[a][b].capacity = c;
		container[a][b].is_forward_direction = 1;
		container[b][a].capacity = c;
		container[b][a].is_forward_direction = 0;
	}
	cin >> S >> D;
	cin.ignore();

	myque temp_que(100);
	
	temp_que.push(S);
	temp_que.push(-1);
	int stack[100];
	int stack_position = -1;
	while (!temp_que.is_empty()){
		int temp_path_value = 0;
		while ((stack[++stack_position] = temp_que.pop()) != -1){ ; };

		temp_path_value = stack[--stack_position];
		for (int i = 0; i < NC; i++){
			if (container[temp_path_value][i].capacity != 0 
				&& isnot_in_stack(stack, i, stack_position)){
				//避免环（相当于之前的visited数组）
			
				//到达指定节点
				if (i == D){
					for (int j = stack_position; j >= 0; j--){
						cout << stack[j] << " ";
					}
					cout << endl;
					stack[++stack_position] = D;
					//计算这条路径的可以增加的流
					int temp_min = 1e6;
					for (int j = 0; j < stack_position;j++){
						//重新：
						//当前流量肯定就是只有一个值，而且是绝对值相等最多就是符号相反，
						//这样就解决当要修改当前流量值的时候无法统一修改的问题
						if (container[stack[j]][stack[j + 1]].is_forward_direction){
							if (temp_min>container[stack[j]][stack[j + 1]].capacity 
								- container[stack[j]][stack[j + 1]].current_flow){

								temp_min = container[stack[j]][stack[j + 1]].capacity 
										   - container[stack[j]][stack[j + 1]].current_flow;
							}
						}
						else{
							if (temp_min>0- container[stack[j]][stack[j + 1]].current_flow){
								temp_min = 0 - container[stack[j]][stack[j + 1]].current_flow;
							}
						}
					}
					//再统一修改当前流量
					for (int j = 0; j < stack_position; j++){
						container[stack[j]][stack[j + 1]].current_flow += temp_min;
						container[stack[j + 1]][stack[j]].current_flow -= temp_min;
					}
					cout << "the temp_min is:"<<temp_min << endl;
					continue;
				}
				//如果没有达到目的地就将下一个节点推入到堆栈中，并将所有的父亲节点也一并推入
				//来达到求解路径的目的，同时以-1作为节点之间的界限。emmm所以要时刻注意队列的容量
				//最好将队列容量设置的大一些。
				for (int j = 0; j <= stack_position; j++)
					temp_que.push(stack[j]);
				temp_que.push(i);
				temp_que.push(-1);
			}	
		}
		stack_position = -1;
	}
	/*
		11
		0 1 3
		0 3 3
		1 2 4
		2 0 3
		2 3 1
		2 4 2
		4 1 1
		3 5 6
		3 4 2
		5 6 9
		4 6 1
		0 6
	*/
	return 0;
}



{%endhighlight%}





