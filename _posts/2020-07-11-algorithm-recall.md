---
layout: post
title: 回溯
categories: [数据结构与算法]
---

## 回溯

回溯，可以用来解决很多很复杂的问题，目前遇到的有排列问题，多次回溯来满足给定的条件问题等。

其中排列问题，又可以分为指数型枚举，组合型枚举和排列型枚举。

* 指数型枚举-是选不选的问题-整个过程是一个**完整的选和不选二叉树**-去重就是剪枝的过程（可以剪掉不选的那一只具体做法就是跳到最后重复的那个位置见四数之和）
* 组合型枚举-是选定哪几个的问题-使用**位运算来进行标记**
* 排列型枚举-是选定所有的然后顺序不同的问题-**也可以使用位运算来进行标记**-过程是一个分支越来越少的树-剪枝过程也是如果有重复的直接跳到最后那个重复的位置

### [力扣-18-四数之和](https://leetcode-cn.com/problems/4sum/)

使用回溯的方法来解决四数之和的问题无法AC，但是这个代码告诉了我们如何对指数型枚举进行去重！

在不选的那个分支上进行剪枝，即是跳到重复的最后一个位置

{% highlight c++ %}
class Solution {
public:
    void get(vector<int>& nums, int index, int target, int tupleLen, 
             int tmp, vector<vector<int>>&res, vector<int> tmpRes)
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
        //在不选的那个分支上进行剪枝，即是跳到重复的最后一个位置
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
        
        get(nums, 0, target, 0, 0, res, tmpRes);
            
        return res;
    }
};
{% endhighlight %}

### [面试题-08-04-幂集](https://leetcode-cn.com/problems/power-set-lcci/)

由于题目中给定的条件是给定的数组中不含重复元素，所以可以使用**指数型枚举的递归**方式来做

而更加直观但是不容易想到的方法是：

[1,2,3] 可以分为000 001 010 011 100 101 110 111  0-7共8中二进制的组合

这样更加直观易懂，但是不易想到！

### [力扣-40-组合总和II](https://leetcode-cn.com/problems/combination-sum-ii/)

这个问题的基本思路还是使用回溯的方法，由于这一次求得也是组合得个数不是一个二叉树，所以也采用数组标记得方法。

关键点还是在于如何去重，这个解决方案得去重代码是放在递归函数后面得while(i+1<nums.size() && nums[i] == nums[i+1])i++;

去重语句放在递归函数得后面意味着，在进行递归得时候我们可以使用一次重复得元素，但是只能使用一次，使用完之后就要跳过

{% highlight c++ %}
class Solution {
public:
    void get(int index, int tmp, int target, vector<int>&nums, 
             vector<bool>& record, vector<int>& tarr, vector<vector<int>>& res)
    {
        if(tmp >= target || index == nums.size())
        {
            if(tmp == target)
                res.push_back(tarr);
            return;
        }
        for(int i = index; i<nums.size(); ++i)
        {
            if(!record[i])
            {
                record[i] = true;
                tarr.push_back(nums[i]);
                get(i+1, tmp+nums[i], target, nums, record, tarr, res);
                record[i] = false;
                tarr.pop_back();
                //这个剪枝就是放在了后面，意思是只能先用一次，用一次之后就要往后退
                while(i+1<nums.size() && nums[i] == nums[i+1])i++;
            }
        }
    }
    vector<vector<int>> combinationSum2(vector<int>& candidates, int target)
    {
        if(candidates.empty())return {};
        sort(candidates.begin(), candidates.end());
        vector<vector<int>> res;
        vector<int> tarr;
        vector<bool> record(candidates.size(), false);
        get(0, 0, target, candidates, record, tarr, res);
        return res;
    }
};
{% endhighlight %}

### [力扣-46-全排列](https://leetcode-cn.com/problems/permutations/)

这个没有难度，因为元素都是不重复的！

### [力扣-47-全排列II](https://leetcode-cn.com/problems/permutations-ii/)
{% highlight c++ %}
vector<bool> record(32, false);
class Solution {
public:
    
    void get(int index, vector<int>& nums, 
             vector<int>& tmp, vector<vector<int>>& res)
    {
        if(index == nums.size())
        {
            res.push_back(tmp);
            return ;
        }

        for(int i = 0; i<nums.size(); ++i)
        {
            if(!record[i])
            {
                int j = nums.size()-1;
                for(j; j>=i; --j)
                {
                    if(nums[j] == nums[i] && !record[j])
                    {//找到了因该使用的那个
                        break;
                    }
                }
                i = j;
                tmp.push_back(nums[i]);
                record[i] = true;
                get(index+1, nums, tmp, res);
                tmp.pop_back();
                record[j] = false;
            }
        }

    }
    vector<vector<int>> permuteUnique(vector<int>& nums) {
        vector<int>tmp;
        vector<vector<int>> res;
        sort(nums.begin(), nums.end());
        get(0, nums, tmp, res);
        return res;
    }
};
{% endhighlight %}



### [力扣-37-解数独](https://leetcode-cn.com/problems/sudoku-solver/)[co][en]

这道题的思想并不难，但是难在细节的处理上，因为这个递归涉及到很多层，所以每一层都要把握好！注意下面代码中的注释行！

{% highlight c++ %}
class Solution {
public:
    bool solve(int i, int j, vector<vector<char>>& board)
    {
        int bsize = board.size();
        //递归终止的地方也很容易出错
        if(i == bsize && j == 0)
        {
            return true;
        }
        else if(j == bsize)
        {
            bool ret = solve(i+1, 0, board);
            //下面这两行看似没有必要，其实是程序提前退出的元凶！
            if(ret)return ret;
            else return false;
        }else{
            if(board[i][j] == '.')
            {
                set<char> con;
                for(int k = 0; k<bsize; ++k)
                {
                    con.insert(board[i][k]);
                    con.insert(board[k][j]);
                }
                for(int m = 0; m<3; ++m)
                {
                    for(int n = 0; n<3; ++n)
                    {
                        con.insert(board[i/3*3+m][j/3*3+n]);
                    }
                }
                for(int k = 0; k<bsize; ++k)
                {
                    if(con.count(k+1+'0')>0)
                        continue;
                    board[i][j] = k+1+'0';
                    bool ret = solve(i, j+1, board);
                    if(ret)return ret;
                    board[i][j] = '.';
                }
            }else{
                bool ret = solve(i, j+1, board);
                if(ret)return ret;
                else return false;
            }
        }
        return false;
    }
    void solveSudoku(vector<vector<char>>& board) {
        cout<<solve(0, 0, board);
        return;
    }
};
{% endhighlight %}

### [力扣-51-N皇后](https://leetcode-cn.com/problems/n-queens/)

N皇后问题是一个普通的回溯问题，但是在处理横、竖、斜的时候要注意处理的细节，这些处理是一块比较多的程序，越多越容易出错，因此越多代码越要清晰！

* 变量定义：用变量n记录当前要填皇后的行，tmp表示正在求得一个解决方案，res放置返回结果
* 函数逻辑：n从0开始，每次get()填一行，如果填入成功进入下一行，否则返回上一层
* 终止条件：如果n == (N皇后的规模)则证明填完了，保存这次结果

{% highlight c++ %}
class Solution {
public:
    void get(int n, vector<string>& tmp, vector<vector<string>>& res)
    {
        if(n == tmp.size())
        {
            res.push_back(tmp);
            return;
        }
        int m = tmp.size();
        for(int i = 0; i<tmp.size(); ++i)
        {
            bool canInsert = true;
            //竖行
            for(int v = 0; v<m && canInsert; ++v)
            {
                if(tmp[v][i] == 'Q')
                    canInsert = false;
            }
            //横行
            for(int h = 0; h<m && canInsert; ++h)
            {
                if(tmp[n][h] == 'Q')
                    canInsert = false;
            }
            //斜行有两个
            int sx = n-1;
            int sy = i-1;
            while(canInsert && sx>=0 && sy>=0)
            {
                if(tmp[sx][sy] == 'Q')
                    canInsert = false;
                sx--;
                sy--;
            }
            sx = n+1;
            sy = i+1;
            while(canInsert && sx<m && sy<m)
            {
                if(tmp[sx][sy] == 'Q')
                    canInsert = false;
                sx++;
                sy++;
            }
            sx = n-1;
            sy = i+1;
            while(canInsert && sx>=0 && sy<m)
            {
                if(tmp[sx][sy] == 'Q')
                    canInsert = false;
                sx--;
                sy++;
            }
            sx = n+1;
            sy = i-1;
            while(canInsert && sx<m && sy>=0)
            {
                if(tmp[sx][sy] == 'Q')
                    canInsert = false;
                sx++;
                sy--;
            }
            if(canInsert)
            {
                tmp[n][i] = 'Q';
                get(n+1, tmp, res);
                tmp[n][i] = '.';
            }
        }
        return;//false;
    }
    vector<vector<string>> solveNQueens(int n) {
        vector<vector<string>> res;
        string oneLine = string(n, '.');
        vector<string> tmp(n, oneLine);
        get(0, tmp, res);
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
||[string]|字符串|
||[graph]|图|
||[dfs]|深度优先搜索|
||[dp]|动态规划|
||[map]|映射表|
||[recu]|递归|
||[reca]|回溯|
||[mems]|记忆化搜索|
||[double-pointer]|双指针|