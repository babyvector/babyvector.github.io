---
layout: post
title: 动态规划
categories: [数据结构和算法]
---

## 一维子串型

一维子串型，是指整个最优化重叠子的过程是一个单方向的过程，一般是从0开始到末尾结束，用dp[i]表示子串[0,i]的最优解。这种类型题是经典的动态规划题型，多用于字符串类型，也可用于具有特定含义的连续数组等。可与二维的子序列类型进行比较学习

### [面试题-17-13-恢复空格](https://leetcode-cn.com/problems/re-space-lcci/)[en][dp][trie字典树]

对于这道题，容易有一个误区，就是上来是不是就应该用二维的dp呢？(很不幸！自己上来就是用二维区间dp，很不容易写)

一般情况下，能用一维的就一定不用二维的，因为维数越少，就越简单；那如何判定一个问题是不是必须使用二维的呢？一般是经验+总结。

目前，自己见过的使用过的二维区间dp的题都有一个显著的特征就是，一个区间的最优解，一定是既依赖区间的左边的内容，又依赖区间右边的内容。而这个题，猛一看，好像是符合，但是仔细分析可以看出来，这个题类似于背包的一个变种，就是从当前index开始，我这个序列应该选多长才能保证f[0-i]得到最优解。所以其最终还是向一个维度上发生变化。

trie字典树+递归+记忆化搜索->不AC
哈希字典集+递归+记忆化搜索->不AC

{% highlight c++ %}
struct Node{
    bool isWord = false;
    map<char, Node> mp;
};
class Solution {
public:
    bool search(Node* pdic, string ins)
    {
        bool ret = false;
        for(int i = 0; i<ins.size(); ++i)
        {
            if(pdic->mp.count(ins[i]) && i == ins.size() -1 && pdic->mp[ins[i]].isWord)ret = true;
            else if(pdic->mp.count(ins[i]))pdic = &pdic->mp[ins[i]];
            else  break;
        }
        return ret;
    }
    map<int, int>f;
    int respaceWithSet(Node* pdic, string& sentence, int index)
    {
        if(index == sentence.size())return 0;
        if(f.count(index))return f[index];
        int i = index+1;
        int minOut = 1e8;
        for(i; i<=sentence.size(); ++i)
        {
            if( search(pdic, sentence.substr(index, i-index)) )
            {
                minOut = min(minOut, respaceWithSet(pdic, sentence, i));
            }else{
                minOut = min(minOut, i-index+respaceWithSet(pdic, sentence, i));
            }
        }
        f[index] = minOut;
        return minOut;
    }
    int respace(vector<string>& dictionary, string sentence) {
        int minLen = 1e8;
        int maxLen = 0;
        Node dic;
        Node* cur = &dic;
        for(auto item:dictionary)
        {
            maxLen = max(maxLen, (int)item.size());
            minLen = min(minLen, (int)item.size());

            cur = &dic;
            for(int i = 0; i<item.size(); ++i)
            {
                if(i == item.size()-1)
                {
                    if(cur->mp.count(item[i]))
                        cur->mp[item[i]].isWord = true;
                    else{
                        cur->mp[item[i]] = Node();
                        cur->mp[item[i]].isWord = true;
                    }
                }
                if(cur->mp.count(item[i]) == 0)
                {
                    cur->mp[ item[i] ] = ( Node());
                    cur = &cur->mp[ item[i] ];
                }else{
                    cur = &cur->mp[item[i]];
                }
            }
        }

        return respaceWithSet(&dic, sentence, 0);
    }
};
{% endhighlight %}

反向trie字典+动态规划->AC

{% highlight c++ %}
struct Node{
    bool isWord = false;
    map<char, Node> mp;
};
class Solution {
public:

    int respace(vector<string>& dictionary, string sentence) {
        int minLen = 1e8;
        int maxLen = 0;
        Node dic;
        Node* cur = &dic;
        for(auto item:dictionary)
        {
            reverse(item.begin(), item.end());

            cur = &dic;
            for(int i = 0; i<item.size(); ++i)
            {
                if(i == item.size()-1)
                {
                    if(cur->mp.count(item[i]))
                        cur->mp[item[i]].isWord = true;
                    else{
                        cur->mp[item[i]] = Node();
                        cur->mp[item[i]].isWord = true;
                    }
                }
                if(cur->mp.count(item[i]) == 0)
                {
                    cur->mp[ item[i] ] = ( Node());
                    cur = &cur->mp[ item[i] ];
                }else{
                    cur = &cur->mp[item[i]];
                }
            }
        }
        
        int ssize = sentence.size();
        vector<int> dp(ssize+1, 1e8);
        Node* pdic = &dic;
        dp[0] = 0;
        for(int i = 1; i<=ssize; ++i)
        {
            int j = i;
            dp[i] = min(dp[i], dp[i-1]+1);
            pdic = &dic;
            while(j-1 >= 0 && pdic->mp.count(sentence[j-1]))
            {
                if(pdic->mp[sentence[j-1]].isWord)
                    dp[i] = min(dp[i], dp[i-(i-j+1)]);
                else 
                    dp[i] = min(dp[i], dp[i-(i-j+1)]+i-j+1);

                pdic = &pdic->mp[sentence[j-1]];
                j--;
            }
        }
        return dp[ssize];
    }
};
{% endhighlight %}


### [力扣-309-最佳买卖股票时机含冷冻期](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

这道题最需要注意的就是，初始值是如何得到的！

{% highlight c++ %}
class Solution {
public:

    int maxProfit(vector<int>& prices) {
        if(prices.size()<=1)return 0;
        vector<vector<int>> f(prices.size()+1, vector<int>(2, 0));
        
        f[0][0] = 0;
        f[0][1] = -prices[0];
        f[1][0] = (prices[1]-prices[0]>0?prices[1]-prices[0]:0);
        f[1][1] = max(-prices[0], -prices[1]);

        for(int i = 2; i<prices.size(); ++i)
        {
            //怎么到手中没有股票的
            f[i][0] = max(f[i-1][0], f[i-1][1]+prices[i]);
            //怎样到手中有股票的
            f[i][1] = max(f[i-1][1], f[i-2][0]-prices[i]);
        }
        return max(f[prices.size()-1][0],f[prices.size()-1][1]);
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

