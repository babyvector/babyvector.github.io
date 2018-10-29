---
layout: post
title: 学术软件|Matlab-MathType
categories: [常用软件]
---


# Matlab

## Matlab中如何进行成段的注释

ctrl+R

## matlab中的kron()函数

C=kron(A,B)函数用于求两个矩阵的Kronecker积，所谓Kronecker积，就是矩阵中的每个元素都乘以矩阵B。
例如：

>>x=[1 2;3 4]
x =
1 2
3 4

>> y=[1 0;0 1]
y =
1 0
0 1

>> kron(x,y)
ans =
1 0 2 0
0 1 0 2
3 0 4 0
0 3 0 4

## matlab中的ones

e=ones(8,1)表示将一个8行1列且元素全为1的矩阵赋值给e，也就是说e是一个列向量，一共8个元素，每个元素都等于1

## matlab中的reshape()

reshape函数重新调整矩阵的行数、列数、维数。
调用格式：

B = reshape(A,m,n)
返回一个m*n的矩阵B， B中元素是按列从A中得到的。如果A中元素个数没有m*n个， 则会引发错误。

B = reshape(A,m,n,p,...)和B = reshape(A,[m n p ...])
返回一个和A具有相同元素的N(不是上面的参数n)维数组。但B的尺寸是m*n*p*...，m*n*p*...必须和prod(size(A))相等。即A和B元素个数相等。

B = reshape(A,...,[ ],...）
计算由占位符[ ]所表示的尺寸的长度，这样维度的乘积等价于prod(size(A))。而prod(size(A))的值必须能被的指定维数的乘积所整除。在这里面占位符[ ] 只能使用一次。

B = reshape(A,siz)
返回一个和A元素相同的n维数组，但是由向量siz来决定重构数组维数的大小，prod(siz)的数量值必须和prod(size(A))的保持一致。

## matlab中squeeze

squeeze：  除去size为1的维度
B = squeeze(A)
描述：
B = squeeze(A)，B与A有相同元素,但所有只有一行或一列的维度（a singleton dimension）被去除掉了。二维阵列不受squeeze影响; 
例如：

src=rand(4,1,3)产生一个均匀分布的阵列，共3页，每页4行1列，经过squeeze后，1列的那个维度就没有了，只剩下4行3列的一个二维阵列。
 
>原文：https://blog.csdn.net/what_lei/article/details/48729573 





















#MathType


## MathType中将字符化为角标的快捷键

**Ctrl+H      上角标**

**Ctrl+L      下角标**

Ctrl+J      上、下角标

Ctrl+F      分式

Ctrl+I      积分

Ctrl+T+S    求和

Ctrl+shift+space   空格

