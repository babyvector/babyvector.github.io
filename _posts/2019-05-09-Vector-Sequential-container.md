---
layout: post
title: Vector-顺序容器
categories: C/C++语言
---

1. Vector-顺序容器的初始化

	构造函数:
		C c;			默认构造函数，构造空容器(array，参见第301页)
		C c1(c2);		构造c2拷贝c1
		C c(b,e);		构造c，将迭代器b和e指定的范围内的元素拷贝到c(array不支持)
		C c{a,b,c,...};	列表初始化c


