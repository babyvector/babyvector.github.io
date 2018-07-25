---
layout: post
title: 求素数的方法
categories: [操作系统]
---
##hashtable
hashtable也叫做散列表
是一种输入与容器位置的一种映射关系
由于容器的大小受限所以最终的我们在进行散列映射的时候可能会出现冲突的情况
而处理映射的时候发生冲突的方法也有很多主要是一下四种思想：
1. 闭散列法
2. 开链法（哈希桶）
3. 素数表
4. 字符串哈希算法


###闭散列法一般又有线性探测发和平方探测法

1. 线性探测发
	通过散列函数hash(key)，找到关键字key在线性序列中的位置，如果当前位置已经有了一个关键字，就产生了哈希冲突，就往后探测hash(key+i)<==><(key+i)%N>个位置（i小于线性序列的大小），直到当前位置没有关键字存在。（\#自己的：增量可以是正可以是负）
2. 平方探测法
	与上的原理相同，只不过探测的时候采用的增量是一个数的平方，同样增量也是可正可负

{% highlight ruby %}
def show
  @widget = Widget(params[:id])
  respond_to do |format|
    format.html # show.html.erb
    format.json { render json: @widget }
  end
end
{% endhighlight %}

```
{% highlight ruby %}
def show
  @widget = Widget(params[:id])
  respond_to do |format|
    format.html # show.html.erb
    format.json { render json: @widget }
  end
end
{% endhighlight %}
```

