---
layout: post
title: 双指针
categories: [数据结构与算法]
---

## 双指针

### [面试题-16-06-最小差](https://leetcode-cn.com/problems/smallest-difference-lcci/)[en][co][double-pointer]

这个是双指针的一个经典应用，不过自己在最开始的时候只知道是用双指针，但是不知道怎样用，导致有工具却不会使的尴尬境地在这个题中双指针的主要思想是：

* 首先使数组有序
* 然后判断该移动哪个指针(实际上使两个指针相互追赶的过程)
* 更新结果

双指针具有一定的灵活性，变通的点在于两个指针的起始位置如何规定，以及如何移动两个指针，其跟滑动窗口有一定相似之处

以下是优秀的代码：

{% highlight c++ %}
class Solution {
public:
    int smallestDifference(vector<int>& a, vector<int>& b) 
    {
        sort(a.begin(),a.end());
        sort(b.begin(),b.end());
        long ret = INT_MAX;
        for(int i = 0, j = 0; i < a.size() && j < b.size();)
        {
            ret = min(ret,abs(long(a[i])-long(b[j])));
            if(a[i] < b[j]){
                i++;
            }else{
                j++;
            }
        }
        return ret;
    }
};
{% endhighlight %}

测试样例：

[0]
[2147483647]

[1,2,12,15]
[4,11,19,23,127,235]

### [面试题-16-21-交换和](https://leetcode-cn.com/problems/sum-swap-lcci/)[double-pointer]

采用双指针的思想使得求解过程更加清晰，虽然耗时不是很理想

代码：
{% highlight c++ %}
class Solution {
public:
    vector<int> findSwapValues(vector<int>& array1, vector<int>& array2) {
        sort(array1.begin(), array1.end());
        sort(array2.begin(), array2.end());
        int sum1 = accumulate(array1.begin(),array1.end(), 0);
        int sum2 = accumulate(array2.begin(),array2.end(), 0);
        for(int i = 0, j = 0; i<array1.size()&&j<array2.size();)
        {
            if(sum1-array1[i]+array2[j] < sum2+array1[i]-array2[j])
            {
                j++;
            }else if(sum1-array1[i]+array2[j] == sum2+array1[i]-array2[j]){
                return {array1[i], array2[j]};
            }else{
                i++;
            }
        }
        return {};
    }
};
{% endhighlight %}

|分类|声明|意义|
|:-:|:-:|:-:|
|困难程度|||
||[hard]|困难|
||[medium]|中等|
||[sample]|简单|
|题目状态和作用|||
||[co]|still confused:仍然有些不清晰的|
||[en]|enlightening:有启发意义的|
||[nf]|not-finished-yet：到目前为止还没有完全靠自己AC的|
|解题思路|||
||[graph]|图|
||[dfs]|深度优先搜索|
||[dp]|动态规划|
||[map]|映射表|
||[recu]|递归|
||[reca]|回溯|
||[mems]|记忆化搜索|
||[double-pointer]|双指针|