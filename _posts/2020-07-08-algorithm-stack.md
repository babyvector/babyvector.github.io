---
layout: post
title: 栈
categories: [数据结构与算法]
---

### [面试题-16-26-计算器](https://leetcode-cn.com/problems/calculator-lcci/submissions/)
这一题自己使用了链表来处理，即优先处理乘除，处理完乘除之后，再处理加减。实际上使用栈来处理这个过程更加简单。即遇到数字就压栈，如果遇到-号就把下一个数字变成-num压入栈，如果遇到乘除号，就直接弹栈之后和后面的一个数字进行运算，运算完之后把结果压栈，最后求和栈中的所有数即可。

{% highlight c++ %}
class Solution {
public:
    int calculate(string s) {
        list<pair<int, bool>> con;
        int pre = 0;
        int first = 0;
        for(first; first<s.size(); first++)
        {
            if(s[first] == '+' || s[first] == '-'||s[first] == '*'||s[first] == '/')
            {
                con.push_back( {atoi( s.substr(pre, first-pre).c_str() ), 0});
                con.push_back({s[first], 1});
                pre = first+1;
            }
        }
        con.push_back( {atoi( s.substr(pre, first-pre).c_str() ), 0});
        auto iter = con.begin();
        while(iter != con.end())
        {
            if( (iter->second) && (iter->first == '*' || iter->first == '/') )
            {
                int tmp = 0;
                auto iterb = iter;
                auto itern = iter;
                iterb--;
                itern++;
                tmp = (iterb)->first*(itern)->first;
                if(iter->first == '/')
                    tmp = (iterb)->first/((itern)->first);
                *itern = {tmp, 0};
                con.erase(iter);
                con.erase(iterb);
                iter = itern;
            }else iter++;
        }
        iter = con.begin();
        while(iter != con.end())
        {
            if( (iter->second) && (iter->first == '+' || iter->first == '-') )
            {
                int tmp = 0;
                auto iterb = iter;
                auto itern = iter;
                iterb--;
                itern++;
                tmp = (iterb)->first-(itern)->first;
                if(iter->first == '+')
                    tmp = (iterb)->first+((itern)->first);
                *itern = {tmp, 0};
                con.erase(iter);
                con.erase(iterb);
                iter = itern;
            }else iter++;
        }
        return con.front().first;
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