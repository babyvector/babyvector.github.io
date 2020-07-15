---
layout: post
title: 数组
categories: [数据结构与算法]
---

### [力扣-31-下一个排列](https://leetcode-cn.com/problems/next-permutation/)

### [力扣-59-螺旋矩阵II](https://leetcode-cn.com/problems/spiral-matrix-ii/)

题解中的方法虽好，但是毕竟不是自己想的，以下是自己通过之前的力扣题-图像旋转得到的

{% highlight c++ %}
class Solution {
public:
    vector<vector<int>> generateMatrix(int n) {
        int count = 1;
        int n2 = n*n;
        vector<vector<int>> res(n, vector<int>(n, 0));
        int j, k;
        for(int i = 0; i<n/2; ++i)
        {
            j = i;
            k = i;
            for(j; j<n-i-1 && count<=n*n; ++j)
                res[k][j] = count++;
            for(k; k<n-i-1 && count<=n*n; ++k)
                res[k][j] = count++;
            for(j; j>i && count<=n*n; --j)
                res[k][j] = count++;
            for(k; k>i && count<=n*n; --k)
                res[k][j] = count++;
        }
        if(n%2)res[n/2][n/2] = n*n;
        return res;
    }
};
{% endhighlight %}