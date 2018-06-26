---
layout: post
title: C/C++中的string
categories: [C/C++]
tags: CBIR
---


## 初始化

1. 拷贝初始化
```
	string s1 = s2;
	string s1 = "value"
```
2. 直接初始化
```
	string s1;//默认初始化，s1是一个空字符串
	string s2(s1);
	string s3("value");
	string s4(n,'c');
```
## string类中的大部分操作

```
os<<s	将s写到输出流os中，**返回os**
is>>s	从is中读取字符串赋给s，字符串以空白分隔，返回is
getline(is,s)	从is中读取一行赋给s返回is
s.empty()	
s.size()
s[n]
s1+s2
s1=s2
s1 == s2
s1 != s2
<,<=,>,>=	利用字符在字典中的顺序进行比较，对字母的大小写敏感
```

### 文件读入


### cctype头文件中   处理string对象中的字符的函数

isalnum(c)			当c是字母或者数字时为真
isalpha(c)			当c是字母时为真
iscntrl(c)			当c为控制字符时为真
isdigit(c)			当c为数字时为真
isgraph(c)			当c不是空格但是可打印时
islower(c)			当c是小写字母时为真
isprint(c)			当c是可打印字符时（c是空格或者具有可视形式）
ispunct(c)			当c是标点符号时为真（c不是控制字符，数字，字母，可打印空白中的一种）
isspace(c)			当从是空白时为真（c是空格，横向制表符，纵向制表符，回车符，换行符，进纸符）
isupper(c)			当c为大写字符时为真
isxdigit(c)			当c是十六进制数字时为真

tolower(c)			如果c是大写字母，输出对应的小写字母；否则原样输出
toupper(c)			如果c是小写字母，输出对应的大写字母；否则原样输出

### for循环对字符串中字符进行处理

for(auto c:s){...}//不会改变原字符串中的字符
for(auto &c:s){...}//可以改变原字符串中的字符//对象绑定

### 访问字符串中的特定位置的字符

1. 采用下标的方法
2. 采用迭代器的方法

