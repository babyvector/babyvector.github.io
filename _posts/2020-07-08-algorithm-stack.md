---
layout: post
title: 栈
categories: [数据结构与算法]
---

## 如何解决栈的问题？

解决栈的问题可以简单的分为两种情况：

* 简单情况：这种情况一般逻辑比较清晰，不涉及深层次的类似递归的栈的处理操作，这种情况下可以使用普通的栈来模拟整个操作

* 复杂情况：这种情况涉及类似栈的递归操作(如：力扣-函数的独占时间)，这种比较复杂的栈操作，如果用普通的栈去模拟操作，会有太多的条件需要考虑。而**函数的调用过程本身就是一个栈操作过程**所以这时我们可以使用函数的递归调用来模拟要实现的栈的操作，这样可以使得逻辑更加优化，代码更加简洁！

### [力扣-636-函数的独占时间](https://leetcode-cn.com/problems/exclusive-time-of-functions/)

{% highlight c++ %}
class Solution {
public:

    int fun(vector<pair<pair<int, int>, string>>& info, int& index, 
            vector<int>& result, vector<pair<pair<int, int>, string>>&handling)
    {
        int regin_length = 0;
        while(index<(int)info.size())
        {
            if(info[index].first.first == handling.back().first.first 
               && info[index].second != handling.back().second)
            {
                int tmp = info[index].first.second - handling.back().first.second+1-regin_length;
                
                result[handling.back().first.first] += tmp;
                handling.pop_back();
                index++;
                return tmp+regin_length;//返回的是时间段
            }else{
                handling.push_back(info[index]);
                regin_length += fun(info, ++index, result, handling);
            }
        }
        return 0;
    }
    vector<int> exclusiveTime(int n, vector<string>& logs) {
        vector<pair<pair<int, int>, string>> info;
        vector<int> result(n,0);
        int lastTime = 0;
        string lastState  = "";
        for(auto& sp:logs)
        {
            stringstream ss(sp);
            string scon[3];
            int count = 0;
            while(getline(ss, scon[count++], ':')){;}
            int id = atoi(scon[0].c_str());
            
            string state = scon[1];
            int time = atoi(scon[2].c_str());
            //将每次的输入分解好，之后再用栈进行处理
            
            info.push_back({{id, time}, state});
        }

        int index = 0;
        while(index<(int)info.size())
        {
            vector<pair<pair<int, int>, string>>handling;
            handling.push_back(info[index++]);
            //使用函数操作代替栈操作，简化代码和逻辑
            fun(info, index, result, handling);
        }

        return result;
    }
};
{% endhighlight %}

### [剑指-Offer-09-用两个栈实现队列](https://leetcode-cn.com/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof/)

### [剑指Offer-30-包含min函数的栈](https://leetcode-cn.com/problems/bao-han-minhan-shu-de-zhan-lcof/)

这个问题需要一个额外的栈来记录当前最小值

### [剑指Offer-31-栈的压入弹出序列](https://leetcode-cn.com/problems/zhan-de-ya-ru-dan-chu-xu-lie-lcof/)

我们可以多使用一个栈来模拟这个过程，如果这个过程有违反栈的操作规则的情况出现则一定是返回false否则返回true

### [面试题-03-01-三合一](https://leetcode-cn.com/problems/three-in-one-lcci/)

由于是用一个数组实现三个栈，一定要注意栈的下标计算问题，来防止栈越界。

另外还要清楚，压栈是先把value存入当前位置后再更改栈顶指针，而弹栈是先改变栈的index然后返回value

对于这个题，特别需要注意的是有可能程序申请的栈的大小为0！

### [面试题-03-03-堆盘子](https://leetcode-cn.com/problems/stack-of-plates-lcci/)

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
||[string]|字符串|
||[graph]|图|
||[dfs]|深度优先搜索|
||[dp]|动态规划|
||[map]|映射表|
||[recu]|递归|
||[reca]|回溯|
||[mems]|记忆化搜索|
||[double-pointer]|双指针|