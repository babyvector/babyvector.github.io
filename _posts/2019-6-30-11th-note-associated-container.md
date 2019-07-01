---
layout: post
title: C++ Primer第11章|关联容器
categories: C/C++语言
---

## 关联容器类型
* 按关键字有序保存元素
	* map					关联数组；保存关键字-值对
	* set					关键字即值，即只保存关键字的容器
	* multimap				关键字可重复出现的map
	* multiset				关键字可重复出现的set
* 无序集合
	* unordered_map			用哈希函数组织的map
	* unordered_set			用哈希函数组织的set
	* unordered_multimap	哈希组织的map；关键字可以重复出现
	* unordered_multiset	哈希组织的set；关键字可以重复出现

### 关键字类型要求

关联容器对其关键字类型有一些限制。

有序容器 map、multimap、set、以及multiset关键字类型必须定义元素的比较方法。默认情况下，标准库使用关键字类型的<运算符来比较两个关键字。（也可以向一个算法提供我们自己定义的比较操做，与之类似，也可以提供自己定义的操作来代替关键字上的<运算符。所提供的操作必须在关键字类型上定义一个**严格弱序**）
	