---
layout: post
title: linux命令
categories: [linux命令积累]
---

## vim

1. vim中的快速查找是

	：/someword    再摁n代表显示下一个

	：？/someword  ..

2. vim中如何进行翻页

	ctrl+f

	ctrl+b

## linux中的expect命令

1. send:用于向进程发送字符
2. expect:从进程中接收字符
3. spawn:启动新的进程
4. interact:允许用户进行交互

例如以下的一个例子：
\#！/usr/bin/expect
spawn su root
expect "Password:"
send "  \r"
send "cd ./6.828/lab\r"
send "ls\r"
interact
\#文件结束
如果想要运行这个sh就要使用命令：
/usr/bin/expect xx.sh
这样做的原因是由于spawn是内嵌在expect内的命令如果不这样做就会出现命令找不到的情况。




