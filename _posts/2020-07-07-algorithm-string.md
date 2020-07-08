---
layout: post
title: 字符串
categories: [数据结构与算法]
---

## 字符串

### [剑指Offer-20-表示数值的字符串](https://leetcode-cn.com/problems/biao-shi-shu-zhi-de-zi-fu-chuan-lcof/)[Medium][hard]
  * 解题方案：递归
  * 优秀方案：
    * 正则表达式
      * "^[-+]?\\d*(?:\\.\\d*)?(?:[eE][+\\-]?\\d+)?$";
    * 状态机

### [力扣-多次搜索](https://leetcode-cn.com/problems/multi-search-lcci/)[medium][medium]

  * 解决方案：暴力匹配
  * 优秀方案：KMP,前缀树

### [力扣-字符串旋转](https://leetcode-cn.com/problems/string-rotation-lcci/)[simple][simple]
  * 解决方案：模拟
  * 优秀方案：(s2+s2).find(s1) == true;

### [剑指Offer-58-翻转单词顺序](https://leetcode-cn.com/problems/fan-zhuan-dan-ci-shun-xu-lcof/)
### [剑指Offer-5-替换空格](https://leetcode-cn.com/problems/ti-huan-kong-ge-lcof/)
### [剑指Offer-30-包含min函数的栈](https://leetcode-cn.com/problems/bao-han-minhan-shu-de-zhan-lcof/)
  * 解题思路：增加一个辅助栈来保存当前栈中最小值  

### [力扣-3-无重复字符的最长子串](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)
  * 最佳解决方案：滑动串口+无序容器 

### [力扣-5-最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)[en]
  这道题最容易想到的就是中心拓展法，但是时间是O(N^2)；但是就算是暴力也能有一个比较清晰的思路，这个题与力扣的最长回文子序列相似，但是最长回文子序列，暴力的话复杂度至少是O(2^N)级别(可以转换为一个子串中的每个字符选或不选的问题，再检查是不是回文)，复杂度起飞，要想AC只有动态规划一条路，自下而上计算，将所有的计算记过保存，来向上迭代，复杂度就能降到O(N^2).
  * 解决方案：动态规划、中心拓展法

### [力扣-6-Z字型变换](https://leetcode-cn.com/problems/zigzag-conversion/)
  由于固定行数了，所以在选字符时可以直接进行计算得到应该选取哪些字符

### [力扣-8-字符串转换整数(atoi)](https://leetcode-cn.com/problems/string-to-integer-atoi/)[en]
  最好的方式是linux底层库中的实现，采用逐位乘10的做法，先用longlong来存放结果，一旦溢出立即返回错误

### [力扣-10-正则表达式匹配](https://leetcode-cn.com/problems/regular-expression-matching/)[en]
  使用递归去写更加容易理解，但是如果样例过复杂就无法AC因为递归的最差的时间复杂度在O(2^N)的水平。但是可以从递归的表达式中递推出动态规划的转移公式，将时间复杂度控制在O(N^2)的水平。

### [力扣-12-整数转罗马数字](https://leetcode-cn.com/problems/integer-to-roman/)

### [力扣-13-罗马数字转整数](https://leetcode-cn.com/problems/roman-to-integer/)

### [力扣-17-电话号码的字母组合](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)
  可以使用哈希表-映射表

### [力扣-20-有效括号](https://leetcode-cn.com/problems/valid-parentheses/)
  可以使用栈

### [力扣-22-括号生成](https://leetcode-cn.com/problems/generate-parentheses/)
  使用回溯法是最有效的

### [力扣-28-实现strStr()](https://leetcode-cn.com/problems/implement-strstr/)

### [力扣-193-有效电话号码](https://leetcode-cn.com/problems/valid-phone-numbers/)

### [力扣-32-最长有效括号](https://leetcode-cn.com/problems/implement-strstr/)[en]
  其实最长有效括号一共就是两种有效的方式
    * ()()
    * (())
  无论是使用dp还是栈都是需要对这两种括号形式进行处理，并不断前进更新最大值，从而实现复杂度O(n)

### [力扣-44-通配符匹配](https://leetcode-cn.com/problems/wildcard-matching/)
  这道题无论是使用递归还是使用动态规划都与正则匹配那个字符串极度相似，但是测试样例复杂了许多

### [力扣-65-有效数字](https://leetcode-cn.com/problems/wildcard-matching/)[hard][nf]
  使用有限状态机(DFA)来做

### [力扣-72-编辑距离](https://leetcode-cn.com/problems/wildcard-matching/)
  与正则表达式、通配符匹配是一样的题，同样可以使用递归和动态规划，递归无法AC，也同样可以通过递归的关系式来推出动态规划的转移方程

### [力扣-76-最小覆盖子串](https://leetcode-cn.com/problems/minimum-window-substring/)[hard]
  使用滑动串口+字典就可以完成

### [力扣-443-压缩字符串](https://leetcode-cn.com/problems/string-compression/)

### [力扣-583-两个字符串的删除操作](https://leetcode-cn.com/problems/string-compression/)
  求得是将两个字符串变成相同字符串得最小删除步数-使用动态规划得方法，也基本上属于子序列问题，可以使用类似前面得正则表达式、通配符、编辑距离中得动态规划得思路来解决。

### [力扣-678-有效的括号字符串](https://leetcode-cn.com/problems/valid-parenthesis-string/)[en]
  从动态规划的角度来讲这道题可以归类于区间dp的范畴，定义dp[i][j]，i，j分别表示区间的起始点和终止点求出最长的有效dp[i][j]后maxlen = j-i+1;不过这道动态规划难就难在如何推导转移公式，思考递推公式的切入点还是从*的三种作用，然后结合递归中的思路来确定最终的转移公式，不过这个转移公式看起来并不简洁，而且初始化也有一些特别需要注意的地方。

  还有一种是从贪心的角度来讲的就是在向前遍历字符串的过程中，随时记录从起始点到当前位置的左括号的最少数量和最多数量（由于*状态是可变的，所以它可以改变到目前为止左括号数量的上下限）

### [剑指-Offer-58-I-翻转单词顺序](https://leetcode-cn.com/problems/fan-zhuan-dan-ci-shun-xu-lcof/)

### [剑指-Offer-67-把字符串转换成整数](https://leetcode-cn.com/problems/ba-zi-fu-chuan-zhuan-huan-cheng-zheng-shu-lcof/)
  参照底层库的写法，处理的非常高效且安全

### [面试题-01-02-判定是否互为字符重排](https://leetcode-cn.com/problems/check-permutation-lcci/)

### [面试题-01-03-URL化](https://leetcode-cn.com/problems/string-to-url-lcci/)

### [面试题-01-04-回文排列](https://leetcode-cn.com/problems/palindrome-permutation-lcci/)

### [面试题-01-05-一次编辑](https://leetcode-cn.com/problems/one-away-lcci/)

### [面试题-01-06-字符串压缩](https://leetcode-cn.com/problems/compress-string-lcci/)

### [面试题-01-09-字符串轮转](https://leetcode-cn.com/problems/string-rotation-lcci/)

### [面试题-05-02-二进制数转字符串](https://leetcode-cn.com/problems/bianry-number-to-string-lcci/)
  这道题是涉及到计算机最基础的东西

### [面试题-08-09-括号](https://leetcode-cn.com/problems/bracket-lcci/)[en][co]
  就是使用回溯进行枚举，但是要注意驱虫

### [面试题-08-14-布尔运算](https://leetcode-cn.com/problems/boolean-evaluation-lcci/)[medium][en][co]
  这道题是本质上就是一个求卡特兰数的一个拓展，题目-96-不同的二叉搜索树[nf].可以使用带记忆的搜索或者是动态规划的方法

### [面试题-10-02-变位词组](https://leetcode-cn.com/problems/group-anagrams-lcci/)

### [面试题-16-08-整数的英语表示](https://leetcode-cn.com/problems/english-int-lcci/)[hard]
  每三位要处理一次就是要注意当0出现的多的时候的细节的处理

### [面试题-16-18-模式匹配](https://leetcode-cn.com/problems/pattern-matching-lcci/)[medium]
  细节上处理比较困难，很容易出现无法漏掉的可能，但是代码实现较长，写起来也比较费时和易错

### [面试题 17.17. 多次搜索](https://leetcode-cn.com/problems/multi-search-lcci/)

### [面试题 17.22. 单词转换](https://leetcode-cn.com/problems/word-transformer-lcci/)[en][dfs][graph]
  这个题的整个搜索过程是一个图的搜索过程，比较奇葩的一点在于，在dfs的过程中如果不进行剪就无法AC，而剪枝的方式就是访问过的结点不再重新恢复没有访问过

困难程度：
[hard]：难
[medium]：中等
[sample]：简单
题目状态和作用：
[co]->still confused:仍然有些不清晰的
[en]->enlightening:有启发意义的
[nf]->not-finished-yet：到目前为止还没有完全靠自己AC的
解题思路：
[graph]
[dfs]
[dp]：动态规划
[map]：映射表
[recu]：递归
[reca]：回溯
[mems]：记忆化搜索


