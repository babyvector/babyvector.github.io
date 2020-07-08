---
layout: post
title: 图|连通图|有向图中使用Tarjan算法找强连通分量的个数
categories: [数据结构与算法]
---

## P3-4


![](../image/tarjan/250px-Tarjan's_Algorithm_Animation.gif)

##关于连通的相关定义

如果两个顶点可以相互连通，则称两个顶点**强连通**（strong connected）.如果有向图G的每两个顶点都强连通，称G是一个**强连通图**。有向图的极大强连通分量，成为**强连通分量（strongly connected components）**,而其他的我们都是称作为**连通分量**