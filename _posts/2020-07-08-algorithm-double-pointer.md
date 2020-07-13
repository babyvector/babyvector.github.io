---
layout: post
title: 双指针
categories: [数据结构与算法]
---

## 双指针

### [力扣-18-四树之和][https://leetcode-cn.com/problems/4sum/][co][en]

这道题使用朴素回溯的方法没法AC，最多能解决一半，只能使用双指针；但是**双指针的去重非常的麻烦和需要技巧**。自己尝试了双指针，能解出大部分解，但是均无法，把所有的去重问题解决掉，以下是看了题解之后自己仿照写的代码。

{% highlight c++ %}
class Solution {
public:
    void get(vector<int>& nums, int index, int target, int tupleLen, int tmp, vector<vector<int>>&res, vector<int> tmpRes)
    {
        if(index == nums.size() && tupleLen == 4)
        {
            if(tmp == target)
            {
                res.push_back(tmpRes);
            }
        }
        if(index == nums.size())return;
        
        int i = 0;
        while(index+i<nums.size() && nums[index] == nums[index+i]){++i;}
        get(nums, index+i, target, tupleLen, tmp, res, tmpRes);

        tmpRes.push_back(nums[index]);
        get(nums, index+1, target, tupleLen+1, tmp+nums[index], res, tmpRes);
        tmpRes.pop_back();
        
    }
    
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        sort(nums.begin(), nums.end());
        vector<vector<int>> res;
        vector<int> tmpRes;
        
        for(int a = 0; a+4<=nums.size(); ++a)
        {
            //去重，抄的
            if(a>0 && nums[a] == nums[a-1])continue;
            for(int b = a+1; b+3<=nums.size(); ++b)
            {
                //去重，抄的
                if(b>a+1 && nums[b] == nums[b-1])continue;
                int ifirst = b+1;
                int ilast = nums.size()-1;
                int sum = nums[a]+nums[b];
                
                while(ifirst < ilast)
                {    
                    if(sum+nums[ifirst]+nums[ilast] == target)
                    {
                        res.push_back({nums[a], nums[b], nums[ifirst], nums[ilast]});
                        //必须在这里去重，抄的
                        while(ifirst < ilast && nums[ifirst] == nums[ifirst+1]){ifirst++;}
                        while(ilast > ifirst && nums[ilast] == nums[ilast-1]){ilast--;}
                        ifirst++;
                        ilast--;
                        
                    }else if(sum+nums[ifirst]+nums[ilast] < target){
                        ifirst++;
                    }else{
                        ilast--;
                    }
                }
            }
        }
        return res;
    }
};
{% endhighlight %}

### [力扣-30-串联所有单词的子串](https://leetcode-cn.com/problems/substring-with-concatenation-of-all-words/)[co][en]

这个题属于双指针的一个**拓展**。具体的方法就是：使用双指针+字符串哈希表O(N)。单独使用哈希表时间复杂度O(N^2)无法通过。
{% highlight c++ %}
class Solution {
public:
    vector<int> findSubstring(string s, vector<string>& words){
        
        if(s == "" || words.empty())return {};
        
        vector<bool> f(words.size(), 0);
        vector<int> res;
        map<string, int> con;
        map<string, int> con_bak;
        int ssize = words[0].size();

        for(int i = 0; i<words.size(); ++i)
        {
            con_bak[ words[i] ]++;
        }
        
        for(int i = 0; i<ssize; ++i)
        {//这一点真的是机智
            int left = i;
            int right = i;
            int count = 0;
            con.clear();//清除不彻底，bug多到数不完
            while(right+ssize <= s.size())
            {
                string tmp = s.substr(right, ssize);
                
                if(con_bak.count(tmp) == 0)
                {   //clear要彻底，一个不能漏
                    right += ssize;
                    left = right;
                    con.clear();
                    count = 0;
                }else{
                    ++con[tmp];

                    if(con[tmp] == con_bak[tmp])
                    {
                        count++;
                    }else if(con[tmp] > con_bak[tmp])
                    {
                        while(con[tmp] != con_bak[tmp])
                        {//shrink时也只要管left而不要两边都管
                            con[s.substr(left,ssize)]--;
                            if( s.substr(left, ssize) != tmp)
                                    count--;
                            left+=ssize;
                        }
                    }
                    //expand要同一不要分别放在else中
                    right += ssize;
                    
                    if(count == con_bak.size())
                    {
                        res.push_back(left);
                        con[s.substr(left, ssize)]--;
                        count--;
                        left = left+ssize;
                    }
                } 
            }
        }

        return res;
    }
};
{% endhighlight %}

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

### [面试题-16-24-数对和](https://leetcode-cn.com/problems/pairs-with-sum-lcci/)

{% highlight c++ %}
class Solution {
public:

    vector<vector<int>> pairSums(vector<int>& nums, int target) {
        if(nums.size()<=1)return {};
        sort(nums.begin(), nums.end());
        vector<vector<int>> res;
        int first = 0;
        int last = nums.size()-1;
        while(first < last)
        {
            if(nums[first] + nums[last] == target)
            {
                res.push_back({nums[first], nums[last]});
                first++;
                last--;
            }else if(nums[first] + nums[last] < target){
                first++;
            }else{
                last--;
            }
        }
        return res;
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