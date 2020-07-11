---
layout: post
title: 回溯
categories: [数据结构与算法]
---

### [力扣-37-解数独](https://leetcode-cn.com/problems/sudoku-solver/)[co][en]
这道题的思想并不难，但是难在细节的处理上，因为这个递归涉及到很多层，所以每一层都要把握好！注意下面代码中的注释行！

{% highlight c++ %}
class Solution {
public:
    bool solve(int i, int j, vector<vector<char>>& board)
    {
        int bsize = board.size();
        //递归终止的地方也很容易出错
        if(i == bsize-1 && j == bsize)
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