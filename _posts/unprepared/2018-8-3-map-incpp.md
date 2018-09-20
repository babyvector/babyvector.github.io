---
layout: post
title: C/C++中的map
categories: [C/C++]
tags: CBIR
---
#map

map<string,int>test_container;

test_container.find();->返回一个iterator，如果找不到...就返回end()

我们可以用map<string,int>::iterator来遍历map

当我们使用test_container[some_string] = ...的时候如果some_string不存在那么就会在map中创建一个新的元素所以当我么进行查找的时候直接使用迭代器进行遍历

map的实现原理是红黑树