---
layout: post
title: 一些常用的电脑和软件快捷键
categories: [电脑和软件相关技巧]
---

## chrome浏览器

1. ctrl+shift+t  恢复刚刚关闭的网页

## vs软件

1. ctrl+k  -> ctrl+c 对选中区域进行注释
2. ctrl+k  -> ctrl+u 对选中区域取消注释

### vs软件中的监视窗口

int * transfer = (int*)malloc(sizeof(int)*10);
//如何在监视的时候一下子查看10个内存中的内容呢？
在框中输入**transfer,10**即可

###VS软件中出现找不到源文件的情况

这是由于导入项目的时候没有将源文件的lib include bin路径导入的项目中，
找到项目->属性->...然后将源文件导入即可。
