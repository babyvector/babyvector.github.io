---
layout: post
title: 数组
categories: [数据结构与算法]
---

### [力扣-31-下一个排列](https://leetcode-cn.com/problems/next-permutation/)

## 前缀和问题

前缀和问题，大部分都是在一个数组中求一个满足条件的区间的问题。通用的解法是在一个数组中有两种类型的数据，开一个数组或者字典，如果遇到同类型的count++,遇到不同类型的count--，这样当count相同的时候说明区间内的两种类型的数据个数相同。然后根据这个性质，可以进一步求解。

### [面试题-17-05-字母与数字](https://leetcode-cn.com/problems/find-longest-subarray-lcci/)

这道题，数据量给到了100000很明显，就算是O(NlogN)的算法也无法AC，所以只能使用O(N)的算法。而前缀和+字典/数组刚好能解决这个问题。

## 数学问题

### [力扣-775-全局倒置与局部倒置](https://leetcode-cn.com/problems/global-and-local-inversions/)

暴力法无法AC，水平适中的选择类似于动态规划的解法，水平高的利用数学原理(这个方法一般很难想到)

## 旋转数组

### [力扣-48-旋转图像](https://leetcode-cn.com/problems/rotate-image/)

下面的代码是一个经典的旋转图像的代码，只消耗O(1)的空间和O(N)的时间复杂度

{% highlight c++ %}
class Solution {
public:
    void rotate(vector<vector<int>>& matrix){
        int n = matrix.size();
        int m = n/2;
        for(int i = 0; i<m; ++i)
        {
            for(int j = i; j<n-i-1; ++j)
            {
                int tmp = matrix[j][i];
                matrix[j][i] = matrix[n-i-1][j];
                matrix[n-i-1][j] = matrix[n-j-1][n-i-1];
                matrix[n-j-1][n-i-1] = matrix[i][n-j-1];
                matrix[i][n-j-1] = tmp;
            }
        }
        return;
    }
};
{% endhighlight %}