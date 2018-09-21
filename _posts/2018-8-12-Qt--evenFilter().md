---
layout: post
title: Qt | evenFilter() 
categories: [Qt]
---

*为了想要编写一个简单的制作演示图片的软件，需要用到Qt中的事件，但是如果只是简单的对鼠标时间进行覆盖，那么等以后按键和鼠标的动作多了之后我们就很难对鼠标动作进行辨别和控制。因此为了长远考虑决定使用eventFilter()引用了下面的网页的博客

>https://www.cnblogs.com/god-of-death/p/7815720.html

鼠标或者键盘上的动作的传递过程如下：

事件过滤器->部件的event函数->部件的事件处理函数

事件过滤器由QObject类中的两个函数来实现。

一个是 installEventFilter，它负责在相应部件上安装事件过滤器，其声明为：

void QObject::installEventFilter(QObject *filterObj);

其中，filterObj参数表示要在其上实现事件过滤器函数的部件。请注意，如果我们在一个部件安装了事件过滤器，一般在其父控件上实现事件过滤器函数。

 

第二个函数是 eventFilter 函数，我们在此函数中实现事件过滤器。请注意：该函数在 QObject 类中声明为一个虚函数，因此只能由 QObject 的子类继承使用