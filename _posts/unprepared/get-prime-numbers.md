---
layout: post
title: 求素数的方法
categories: [操作系统]
---

1. 试除法
首先假定一个正常的数num
首先素数肯定在2-num/2之间
然后除了2素数肯定是奇数
将所求的素数缓存用新数字除缓存的素数以判断新的素数

2. 筛选法

也是以空间换取时间
不过是将所有素数的倍数排除缓存接着求
可以用bool量来进行代表是否为素数，而用下表代表数字