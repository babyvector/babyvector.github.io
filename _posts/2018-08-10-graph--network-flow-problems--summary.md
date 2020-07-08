---
layout: post
title: 图-网络流问题
categories: [数据结构与算法]
---

## 网络流问题（network flow problems）

In combinatorial optimization（组合优化问题）, network flow problems are a class of computational problems（计算问题） in which the input is a flow network (a graph with numerical capacities on its edges《图的定义》), and the goal is to construct a flow, numerical values on each edge that respect the capacity constraints（容量限制） and that have incoming flow equal to outgoing flow（输入等于流出） at all vertices except for certain designated terminals.（除了一些特殊的例子）

## 网络流问题一般分为以下几个问题

Specific types of network flow problems include:

* The **maximum flow problem(最大流问题)**, in which the goal is to maximize the total amount of flow out of the source terminals and into the sink terminals
* The **minimum-cost flow problem(最小费用流问题)**, in which the edges have costs as well as capacities and the goal is to achieve a given amount of flow (or a maximum flow) that has the minimum possible cost
* The **multi-commodity flow problem(多物网络流问题)**, in which one must construct multiple flows for different commodities whose total flow amounts together respect the capacities（是多种物品（或货物）在网络中从不同的源点流向不同的汇点的网络流问题）
* **Nowhere-zero flow**, a type of flow studied in combinatorics in which the flow amounts are restricted to a finite set of nonzero values

## 常用的解决算法

Algorithms for constructing flows include：

* Dinic's algorithm, a strongly polynomial（多项式） algorithm for maximum flow
* The Edmonds–Karp algorithm, a faster strongly polynomial algorithm for maximum flow
* The Ford–Fulkerson algorithm, a greedy algorithm for maximum flow that is not in general strongly polynomial
* The network simplex algorithm, a method based on linear programming but specialized for network flow
* The out-of-kilter algorithm for minimum-cost flow
* The push–relabel maximum flow algorithm, one of the most efficient known techniques for maximum flow


