---
layout: post
title: 树 | B树-B*树-B+树 
categories: [数据结构与算法]
---

# B+tree

Insertion
* Perform a search to determine what bucket the new record should go into.
* If the bucket is not full (at most {\displaystyle b-1} b-1 entries after the insertion), add the record.
* Otherwise, before inserting the new record
* split the bucket.
* original node has ⎡(L+1)/2⎤ items
* new node has ⎣(L+1)/2⎦ items
* Move ⎡(L+1)/2⎤-th key to the parent, and insert the new node to the parent.
* Repeat until a parent is found that need not split.
* If the root splits, treat it as if it has an empty parent and split as outline above.
**B-trees grow at the root and not at the leaves.[1](由页节点向根部生长)**
