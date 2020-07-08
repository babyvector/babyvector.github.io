---
layout: post
title: 动态规划-贪心-递归
categories: [数据结构与算法]
---


动态规划：	dp[] 、dp[][] dp[][][]等 大问题的最优解可以由小问题的最优解推出

递归	：	fun();{func();}

贪心：	现实生活中花钱

## 1. 动态规划
	
* 定义：通过把原问题分解为相对简单的子问题的方式求解复杂问题的方法。动态规划常常适用于有重叠子问题和最优子结构性质的问题。
* 缺点：动态规划需要很大的空间以存储中间产生的结果，这样可以使包含同一个子问题的所有问题共用一个子问题解，从而体现动态规划的优越性，但这是以牺牲空间为代价的，为了有效地访问已有结果，数据也不易压缩存储，因而空间矛盾是比较突出的。
* 适用条件：能将大问题拆成几个小问题，且满足无后效性、最优子结构性质。


### 动态规划之求最大子列和问题

#### 思路：

动态规划：通过把原问题分解为相对简单的子问题的方式求解复杂问题的方法。动态规划常常适用于有重叠子问题和最优子结构性质的问题。

从题目给出例题输入来找规律，我们需要一个head来指向最优解序列的头结点，head指向将不断变化，定义一个最优解数组dp[i]记录截至到当前元素的最优子序和.

输入: [-2,1,-3,4,-1,2,1,-5,4],

输出: 6

解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。

初始化dp[0] = -2

-2 : dp[0] = -2, head = 0

1 : dp[1] = 1 head = 1

-3 : dp[2] = -2 head = 1

通过前三项可以总结出规律：dp[i] = max(dp[i-1] + nums[i], nums[i]) 如果选中当前项，则更改head指向当前项

4 : dp[3] = max(dp[2] + 4, 4) = max(-2, 4) = 4 head = 3

-1: dp[4] = max(dp[3] +-1 , -1) = max(3, -1) = 3 head不变

2： dp[5] = max(5, 2) = 5, head 不变

1 : dp[6] = 6 head不变

-5 ： dp[7] = 1 head不变

4 : dp[8] = 5 head不变

我们只需要其中最大的子序号，遍历一遍dp数组找出最大项dp[i]，并且从 head 到 i 的序列就是最大子序列

#### 代码：

{%highlight ruby%}
class Solution {
    public int maxSubArray(int[] nums) {

        if(nums.length == 0) return 0;
        if(nums.length == 1) return nums[0];

        int []dp = new int[nums.length];
        int head = 0;
        dp[0] = nums[0];

        for(int i = 1; i < nums.length; i++){
            dp[i] = Math.max(dp[i-1] + nums[i], nums[i]); //核心代码
            if(dp[i-1] + nums[i] < nums[i])
                head = i;
        }

        int max = dp[0]; //找最大和
        for(int i = 0; i < dp.length; i++){
            if(max < dp[i])
                max = dp[i];
        }
        
        return max;
        
    }
}
{%endhighlight%}
**在推导dp的过程中max()函数就是相当于在剪枝，当然我们在使用递归的时候是不会剪纸，所以运行时间指数增加**

>链接：https://leetcode-cn.com/problems/two-sum/solution/dong-tai-gui-hua-qiu-jie-zui-da-zi-xu-he-by-hikes/
>来源：力扣（LeetCode）

>https://www.zhihu.com/question/23995189/answer/613096905(这个讲的动态规划比较清晰)

### 最长有效括号

以下是自己的思路讲解：
假设例题是


>https://leetcode-cn.com/problems/longest-valid-parentheses/

## 2. 贪心

永远只要求现在是最好的（鼠目寸光）

## 3. 递归

看过一个比较形象的讲解就是：给你一个迷宫图让你从这个迷宫图中找到从入口到出口的一条路，这个时候最好的方式就是从迷宫的出口向入口开始找，这样找是最快的。

## 一个例子区分动态规划和贪心算法

>https://www.zhihu.com/question/23995189

使用一个凑指定数目的钱的一个问题：

货币只有1,5,11三种，请使用最少数量的货币凑出15元来

* 贪心算法
	11，1，1，1，1
* 动态规划
	5，5，5
接下来讲解以下动态规划：
{%highlight ruby%}
#include<stdio.h>
#define MONEY_TYPE 3
#define MAX_MONEY 15
int main(){
	int m_y[MONEY_TYPE];
	int dp[MAX_MONEY+1];
	m_y[0] = 1;
	m_y[1] = 5;
	m_y[2] = 11;//默认由小到大排列 
	for(int i = 0;i<MAX_MONEY+1;i++){
		dp[i] = 0;
	}
	for(int i = 1;i<MAX_MONEY+1;i++){
		
			int temp_min =10000;
			for(int j = MONEY_TYPE-1;j>=0;j--){
				if(i>=m_y[j]&&temp_min>dp[ i-m_y[j] ] +1){
					temp_min = dp[ i-m_y[j] ] +1;
				}
			}
			dp[i] = temp_min;
	
	}
	for(int i = 1;i<MAX_MONEY+1;i++){
		printf("dp[%d]:%d\n",i,dp[i]);
	
	}
	return 0;
}
{%endhighlight%}

//动态规划的过程中进行了动态剪枝，但是递归并没有做动态剪枝



## 动态规划使用条件的稍微详细的解释

【无后效性】　　一旦f(n)确定，“我们如何凑出f(n)”就再也用不着了。　　要求出f(15)，只需要知道f(14),f(10),f(4)的值，而f(14),f(10),f(4)是如何算出来的，对之后的问题没有影响。　　“未来与过去无关”，这就是无后效性。　　（严格定义：如果给定某一阶段的状态，则在这一阶段以后过程的发展不受这阶段以前各段状态的影响。）

【最优子结构】　　回顾我们对f(n)的定义：我们记“凑出n所需的最少钞票数量”为f(n).　　f(n)的定义就已经蕴含了“最优”。利用w=14,10,4的最优解，我们即可算出w=15的最优解。　　**大问题的最优解可以由小问题的最优解推出**，这个性质叫做“最优子结构性质”。　　引入这两个概念之后，我们如何判断一个问题能否使用DP解决呢？　　能将大问题拆成几个小问题，且满足无后效性、最优子结构性质。

>作者：阮行止
>链接：https://www.zhihu.com/question/23995189/answer/613096905
