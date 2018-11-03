---
layout: post
title: linux命令
categories: [linux命令积累]
---

## vim

1. 查找
```
	：/someword    再摁n代表显示下一个
	：？/someword  ..
```
2. 翻页
```
	ctrl+f
	ctrl+b
```
3. 撤销
```
	U
   vim中的前进命令
	ctrl+r
```

4. 移动光标到首行和末行
```
	gg将光标移动到首行
	G将光标移动到最后一行
```
5. 显示行号
```
	set number
```
6. 剪切、复制模式
```
	v进入剪切模式下 
	x剪切
	p粘贴
```
7. 多窗口
```
- 打开多个窗口
	打开多个窗口的命令以下几个：
	
	横向切割窗口
		:new+窗口名(保存后就是文件名)
		:split+窗口名，也可以简写为:sp+窗口名
	纵向切割窗口名
		:vsplit+窗口名，也可以简写为：vsp+窗口名
- 关闭多窗口
	可以用：q!，也可以使用：close，最后一个窗口不能使用close关闭。
	使用close只是暂时关闭窗口，其内容还在缓存中，只有使用q!、w!或x才能真能退出。
	
	:tabc 关闭当前窗口
	:tabo 关闭所有窗口
- 窗口切换
	:ctrl+w+j/k，通过j/k可以上下切换
	或者:ctrl+w加上下左右键，还可以通过快速双击ctrl+w依次切换窗口。
- 窗口大小调整
	纵向调整
		:ctrl+w + 纵向扩大（行数增加）
		:ctrl+w - 纵向缩小 （行数减少）
		:res(ize) num  例如：:res 5，显示行数调整为5行
		:res(ize)+num 把当前窗口高度增加num行
		:res(ize)-num 把当前窗口高度减少num行
	横向调整
		:vertical res(ize) num 指定当前窗口为num列
		:vertical res(ize)+num 把当前窗口增加num列
		:vertical res(ize)-num 把当前窗口减少num列
- 给窗口重命名
	:f file
```
8. vi打开多文件
```
	vi a b c
	:n 跳至下一个文件，也可以直接指定要跳的文件，如:n c，可以直接跳到c文件
	:e# 回到刚才编辑的文件
```
9. 文件浏览
```
	:Ex 开启目录浏览器，可以浏览当前目录下的所有文件，并可以选择
	:Sex 水平分割当前窗口，并在一个窗口中开启目录浏览器
	:ls 显示当前buffer情况
```
10. vi与shell切换
```
	:shell 可以在不关闭vi的情况下切换到shell命令行
	:exit 从shell回到vi
```

## shell用法
1. shell脚本中最常用的命令
```
	if
	for
	while
	$(())
```
2. 常见运算和操作.
```
	1. 使用let用法举例如下
		let fileNumber--                 #let fileNumber=$fileNumber-1
	2. 使用expr用法如下
		Expr expression1 操作符expression2
		操作符必须加’\’用于转义，并且操作符和两个expression之间必须有空格（这一点与let不同）且Expr 不适合小数的运算。
	
	3. 常见的算数操作分类：
		加法：+
		减法：-
		乘法：*
		除法：/
		取余数：%
		res1=`expr $num1 \+ $num2 \+ $num3`  
		res2=`expr $num1 \- $num2 \- $num3`  
		res3=`expr $num1 \* $num2 \* $num3`  
		res4=`expr $num1 \/ $num2 \/ $num3`  
		res5=`expr $num1 \% $num2 \% $num3` 
```
3. shell截取字符串.
```
	${var#*/}//比较常用
	${var##*/}
	${var%/*}
	${var%%/*}
	${var:start:len}//比较常用
	${var:start}//比较常用 
	${var:0-start:len}
	${var:0-start}
	https://www.cnblogs.com/fengbohello/p/5954895.html
```
4. if语法.
	- if简单语法规则
	```
		if [ command ];then
		   符合该条件执行的语句
		elif [ command ];then
		   符合该条件执行的语句
		else
		   符合该条件执行的语句
		fi
	```
	- 常见错误.
	```
		if[   f [之间一定要有空格否则怎么都没法排除错误
		if [ "$string1" == "$string2" ]
			 == 是用于判断字符串是否相等，-eq判断的（应该）是数字是否相等
	```
	- 文件/文件夹（目录）判断
	```
		[ -b FILE ] 如果 FILE 存在且是一个块特殊文件则为真。
		[ -c FILE ] 如果 FILE 存在且是一个字特殊文件则为真。
		[ -d DIR ] 如果 FILE 存在且是一个目录则为真。
		[ -e FILE ] 如果 FILE 存在则为真。
		[ -f FILE ] 如果 FILE 存在且是一个普通文件则为真。
		[ -g FILE ] 如果 FILE 存在且已经设置了SGID则为真。
		[ -k FILE ] 如果 FILE 存在且已经设置了粘制位则为真。
		[ -p FILE ] 如果 FILE 存在且是一个名字管道(F如果O)则为真。
		[ -r FILE ] 如果 FILE 存在且是可读的则为真。
		[ -s FILE ] 如果 FILE 存在且大小不为0则为真。
		[ -t FD ] 如果文件描述符 FD 打开且指向一个终端则为真。
		[ -u FILE ] 如果 FILE 存在且设置了SUID (set user ID)则为真。
		[ -w FILE ] 如果 FILE存在且是可写的则为真。
		[ -x FILE ] 如果 FILE 存在且是可执行的则为真。
		[ -O FILE ] 如果 FILE 存在且属有效用户ID则为真。
		[ -G FILE ] 如果 FILE 存在且属有效用户组则为真。
		[ -L FILE ] 如果 FILE 存在且是一个符号连接则为真。
		[ -N FILE ] 如果 FILE 存在 and has been mod如果ied since it was last read则为真。
		[ -S FILE ] 如果 FILE 存在且是一个套接字则为真。
		[ FILE1 -nt FILE2 ] 如果 FILE1 has been changed more recently than FILE2, or 
							如果 FILE1 exists and FILE2 does not则为真。
		[ FILE1 -ot FILE2 ] 如果 FILE1 比 FILE2 要老, 或者 FILE2 存在且 FILE1 不存在则为真。
		[ FILE1 -ef FILE2 ] 如果 FILE1 和 FILE2 指向相同的设备和节点号则为真。
		-e FILE 测试文件是否存在
	    -f file 测试文件是否为普通文件
	    -d file 测试指定路径是否为目录
	    -r file 测试文件对当前用户是否可读
	    -w file 测试文件对当前用户是否可写
	    -x file 测试文件对当前用户是都可执行
	```
	- 字符串判断
	```
		[ -z STRING ] 如果STRING的长度为零则为真 ，即判断是否为空，空即是真；
		[ -n STRING ] 如果STRING的长度非零则为真 ，即判断是否为非空，非空即是真；
		[ STRING1 = STRING2 ] 如果两个字符串相同则为真 ；
		[ STRING1 != STRING2 ] 如果字符串不相同则为真 ；
		[ STRING1 ]　 如果字符串不为空则为真,与-n类似
		-z string 测试指定字符是否为空，空着真，非空为假
	    -n string 测试指定字符串是否为不空，空为假 非空为真
	```
	- 数值判断
	```
		INT1 -eq INT2           INT1和INT2两数相等为真 ,=
		INT1 -ne INT2           INT1和INT2两数不等为真 ,<>
		INT1 -gt INT2            INT1大于INT1为真 ,>
		INT1 -ge INT2           INT1大于等于INT2为真,>=
		INT1 -lt INT2             INT1小于INT2为真 ,<</div>
		INT1 -le INT2             INT1小于等于INT2为真,<=
	```
	- 整数比较
	```
		-eq 等于,如:if [ "$a" -eq "$b" ]
		-ne 不等于,如:if [ "$a" -ne "$b" ]
		-gt 大于,如:if [ "$a" -gt "$b" ]
		-ge 大于等于,如:if [ "$a" -ge "$b" ]
		-lt 小于,如:if [ "$a" -lt "$b" ]
		-le 小于等于,如:if [ "$a" -le "$b" ]
		大于(需要双括号),如:(("$a" > "$b"))
		>= 大于等于(需要双括号),如:(("$a" >= "$b"))
		小数据比较可使用AWK
	```
	- 字符串比较
	```
		== 等于  两边要有空格
	    != 不等
	    >  大于
	    <  小于
		= 等于,如:if [ "$a" = "$b" ]
		== 等于,如:if [ "$a" == "$b" ],与=等价
		注意:==的功能在[[]]和[]中的行为是不同的,如下:
		1 [[ $a == z* ]] # 如果$a以"z"开头(模式匹配)那么将为true
		2 [[ $a == "z*" ]] # 如果$a等于z*(字符匹配),那么结果为true
		3
		4 [ $a == z* ] # File globbing 和word splitting将会发生
		5 [ "$a" == "z*" ] # 如果$a等于z*(字符匹配),那么结果为true 
	```
	- 复杂逻辑判断
	```
		-a 与
		-o 或
		! 非
		&&
		||
		exp1: 如果a>b且a<c
		if (( a > b )) && (( a < c ))
		或者
		if [[ $a > $b ]] && [[ $a < $c ]]
		或者
		if [ $a -gt $b -a $a -lt $c ]
		
		exp2:如果a>b或a<c
		if (( a > b )) || (( a < c ))
		或者
		if [[ $a > $b ]] || [[ $a < $c ]]
		或者
		if [ $a -gt $b -o $a -lt $c ]
	 
		注："||"和"&&"在SHELL里可以用，也就是第一个写成if [ a>b && a<c]
	```
5. shell与用户交互.
```
	if利用read传参判断
	举例子：查看分数	
	#!/bin/bash
	#echo -n "please input your score:"
	#read score
	#echo "input score is $ score "
	read -p "please input a score:" score
	echo -e "your score [$score] is judging by sys now"
	if [ "$score" -ge "0" ]&&[ "$score" -lt "60" ];then
	        echo "sorry,you are lost!"
	elif [ "$score" -ge "60" ]&&[ "$score" -lt "85" ];then
	        echo "just soso!"
	elif [ "$score" -le "100" ]&&[ "$score" -ge "85" ];then
	        echo "good job!"
	else
	        echo "input score is wrong , the range is [0-100]!"
	fi
```
6. 屏蔽shell文本中成段文本
```
	:<<EOF
	code...
	EOF
```
7. 使用shell脚本中出现的问题let：not found
```
   解决方法：http://blog.csdn.net/u011630575/article/details/77197349
```
> https://www.cnblogs.com/aaronLinux/p/7074725.html（条理清晰）

## ls

```
ls -lR  ->R代表子目录
ls -l|find *|wc -l
```
## mkdir
```
	1.嵌套建立文件夹
	mkdir -p /home/xuyongkang/test   ->直接建立嵌套的文件夹
	mkdir test1 test2 
```


## ps

ps命令用于查看系统中正在运行的进程
```
参数格式：
- e 显示所有进程。
- f 全格式。
- h 不显示标题。
- l 长格式。
- w 宽输出。
- a 显示终端上的所有进程，包括其他用户的进程。
- r 只显示正在运行的进程。
- x 显示没有控制终端的进程。
```

最常用的几个命令
```
- ps -ef 查看全格式的全部进程
- ps -ax 查看全部进程
- ps -ef|grep <进程名> 查看并筛选 跟进程名有关的进程，该进程名可以是进程的全部或者部分
```
例子
```
ps -ef|grep nginx
```
defunct
```
	表示一个进程僵尸，僵尸进程的父亲进程的查看方式
	ps -ef | grep defunct_process_pid（从网络上查看的）
```
## &
```
	command&  将命令放入后台运行
```
## jobs
```
jobs -l显示所有的后台运行进程
$jobs 
	[2] + running tar tv3 * & 
	[1] - running find/ -name README -print > logfile& 
```
## fg
```
	jobs是和fg配合使用的
	$ fg %find 
	find/-name README -print > logfile 
	注意,显示的命令行末尾没有&符号.下面的命令能产生同样的效果: 
	$ fg %1
```
## bg
```
	它是把前台进程换到后台执行,其使用格式是: 
	bg [job...] 
	其中,job还是一个或多个进程的PID,命令名称或作业号,在参数前要带%号.例如,在cc(C编译命令)命令执行过程中,按 
	Z键,使作业挂起.然后键入以下命令: 
	$ bg %cc 
	该挂起的作业在后台重新开始运行.........  <<<这个非常的重要能够经常的用到>>>
```
## export
    export的作用是增加系统的环境变量
	CPLUS_LIBRARY_PATH -> c++编译的环境变量的路径
	LD_LIBRARY_PATH ->动态链接库的环境变量的路径
	例子：
	export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/root/data
## top htop atop
```
    top动态显示进程的运行状况
    htop动态显示内存和CPU的占用情况
    atop动态显示内存CPU和GPU的使用情况
```
## shuf 抽取随机数
```
	shuf train.txt -o test.txt -n 30 从train中随机选取30行导入到test中
```
## history
```
	查看历史记录
    history 1000 >./history.txt    ->将1000条的历史命令放入到history.txt中
```
## apt-get
```
    sudo apt-get clean 清理以前安装过的文件
    sudo apt-get update 更新安装的软件
	sudo apt-get upgrade 对软件进行升级
	apt-get install packagename
		安装一个新软件包（参见下文的aptitude）
	apt-get remove packagename
		卸载一个已安装的软件包（保留配置文档）
``` 
## find
	使用方法
```  
	find [path] [option] [expression]
```
	例子
```
	find ./total/ -name "*.tif" -exec cp {} /amyfile/train/ \;
	这里加入了\;这样才能正常运行否则就不能正常的运行。
	这样做能构保证当复制大量的文件的时候不会出现参数列表过长出现无法复制的情况，
	因为使用普通的mv和cp的时候因为参数列表没法进行复制。
```
	（0）限定搜索的层数
```	
		find ./ -maxdepth 1 搜索本地目录，限定搜索的层数最大层数为测层 
```
	（1）反向查找
```
		除了查找满足条件的文件之外，我们还可以查找不满足条件的所有文件。当我们知道要在查找中排除哪些文件时，这个选项就能发挥作用了。	
		find ./test -not -name "*.php"
```
	（2）结合多个查找条件
```		
		我们可以同时使用多个查找条件来指定文件名并排除某些文件。
		find ./test -name 'abc*' ! -name '*.php'
```   	
	（3）只查找文件或目录
```
		有时我们只想通过某个名字查找对应的文件或对应的目录，我们可以很容易实现这个要求。
		$ find ./test -name abc*
		./test/abc.txt
		./test/abc
	    只查找文件
		$ find ./test -type f -name "abc*"
		./test/abc.txt
	    只查找目录
		$ find ./test -type d -name "abc*"
		./test/abc
```
	（4）查找属于特定用户的文件
```
		查找当前目录下，属于 bob 的文件。	
		$ find . -user bob
		查找属于特定用户组的文件
		# find /var/www -group developer
```
	（5）查找过去的第 N 天被修改过的文件
```
		# find / -mtime 50
```
	（6） 查找过去的 N 分钟内状态发生改变的文件
```
		$ find /home/bob -cmin -60
```
	（7）基于文件大小的查找
```
		查找指定大小的文件
		$ find / -size 50M
		 查找大小在一定范围内的文件
		$ find / -size +50M -size -100M
```
	（8）高级操作

	(9) 结合-exec
```
		我们使用 find 命令找到文件后，只能看到文件路径。如果想进一步查看文件信息，可以结合 ls 命令来实现。	
		$ find . -exec ls -ld {} \;
		drwxrwxr-x 4 enlightened enlightened 4096 Aug 11 19:01 . 
		-rw-rw-r-- 1 enlightened enlightened 0 Aug 11 16:25 ./abc.txt
		drwxrwxr-x 2 enlightened enlightened 4096 Aug 11 16:48 ./abc
		drwxrwxr-x 2 enlightened enlightened 4096 Aug 11 16:26 ./subdir
		-rw-rw-r-- 1 enlightened enlightened 0 Aug 11 16:26 ./subdir/how.php
		-rw-rw-r-- 1 enlightened enlightened 29 Aug 11 19:13 ./abc.php
		-rw-rw-r-- 1 enlightened enlightened 0 Aug 11 16:25 ./cool.php
```

```
		删除找到的文件
		下面的命令会删除 tmp 目录下扩展名为 .txt 的文件。
		$ find /tmp -type f -name "*.txt" -exec rm -f {} \;
		我们同样可以删除目录，只要把 -type 后面的 f 改为 d ，并且在 rm 命令后面加上 -r 即可。
		$ find /tmp -type d -name "dirToRemove" -exec rm -r -f {} \;
```

## head
```
	输出文件的前3000行
	head -n 3000 test.txt >temp.txt
```
## eval 执行字符串中内容
```
    eval  使用方法
	command1='firstPicName="06_"$firstDirCount".tif"'
	eval command1;
	这个时候相当于执行了一句firstPicName="06_"$firstDirCount".tif"
```

## ssh
```
	远程登录
	ssh root@47.92.83.74 -p 46907
```
## scp
``` 
	从远程端口拷贝到本地
		scp -P 46907 root@47.92.83.74:/root/NETLearning/out/out_iter_500000.caffemodel  
										/home/xuyongkang/atest/out_iter_500000.caffemodel
		scp -P 46907 root@47.92.83.74:文件路径1 文件路径2
	从本地拷贝到远程端口
		scp -P 46907 /home/xuyongkang/atest/NETLearing.tar.gz root@47.92.83.74:/temp
```
## cmp 比较文件
```
比较任意两个类型的文件，把结果输出到标准输出上
默认：文件相同不输出
	 不同文件输出不同
参数：
	-c显示不同的信息
	-l列出所有的不同信息
	-s错误信息不提示
```
## diff 比较文件内容
```
作用：
	用于比较文件内容，找到不同的地方
输出：
	diff程序的输出被成为补丁(patch)，linux系统中有一个patch命令，可以根据diff的输出将a文件内容改成b。这个功能非常有用，比如打补丁。
命令参数：
	-a diff预设会逐行比较文本文件
	-b或者--ignore-blank-change 不检查空格字符的不同
	-B或者--ignore-blank-lines  不检查空白行
	-c 显示全部内文，并标出不同之处
	-p 若比较文件为C语言的程序文件时，显示差异所在的函数名称

```
## tar
```
	tar -xzvf X.tar.gz 解压*.tar.gz文件
	tar -xvf *.tar
	gunzip *.gz
	bzip2 *.bz2
	tar -xjf *tar.bz2
	uncompress *.Z
	tar -xZf *.tar.Z
	unrar e *.rar
	unzip *.zip
```
## wc 查看文件中的行数或者字数
```
	用法：wc
		[选项]...[文件]...
		-c打印字节数
		-m打印字符数
		-l打印换行数
		-L显示最长行的长度
		-w统计单词个数
		eg.wc file1 file2
```
## grep sed awk  
```
	其中文本文件非常重要，因为unix提供 很多了实用程序（utility），这些程序功能相对独立，
	但可以通过管道来实现进程间通信（IPC），这样松散的耦合可以完成复杂的操作。
	grep搜索
	sed 修改和编辑文本文件中某些行
	awk 是访问文本文件，操纵文本文件中某些数据
```
## echo 
```
	echo 中  \b表示退格的意思因此也就可以实现原位置显示百分比了。
	#eg:start#
	cat dirname.txt|while read line
	do

		echo -n $line >>dirnameandnum.txt
		echo -n "#">>dirnameandnum.txt
		find $line |wc -l >>dirnameandnum.txt

		#echo -en "\b\b\b\b\b\b\b\b\b\b" `echo $line` 
	done
	#eg:end#
	-n 代表不换行输出到文本文件中否则则进行换行
```

## 正则表达式
```
	^：表示匹配开始
	$:表示匹配结束
	[A-Za-z0-9]匹配
	0\{0,2\}:表示有0到2个0
>https://www.cnblogs.com/Quincy/archive/2012/03/26/2418485.html
```
## 查看磁盘情况
```
	df -hl  
	df -m是以M的形式显示容量b k m g t
	   -h是以人性化形式显示
```

## ping
```
	ping -c 10 -i 1 www.baidu.com
		64 bytes from 111.13.100.92: icmp_seq=1 ttl=53 time=16.6 ms
		64 bytes from 111.13.100.92: icmp_seq=2 ttl=53 time=15.5 ms
		64 bytes from 111.13.100.92: icmp_seq=3 ttl=53 time=15.5 ms
		64 bytes from 111.13.100.92: icmp_seq=4 ttl=53 time=15.5 ms
		64 bytes from 111.13.100.92: icmp_seq=5 ttl=53 time=16.2 ms
		64 bytes from 111.13.100.92: icmp_seq=6 ttl=53 time=14.6 ms
		64 bytes from 111.13.100.92: icmp_seq=7 ttl=53 time=14.6 ms
		64 bytes from 111.13.100.92: icmp_seq=8 ttl=53 time=16.1 ms
		64 bytes from 111.13.100.92: icmp_seq=9 ttl=53 time=15.7 ms
		64 bytes from 111.13.100.92: icmp_seq=10 ttl=53 time=15.5 ms

		10 packets transmitted, 10 received, 0% packet loss, time 9015ms
		icmpICMP是“Internet Control Message Protocol”（Internet控制消息协议）的缩写。
		它是TCP/IP协议族的一个子协议，用于在IP主机、路由器之间传递控制消息
	
```
## netstat

netstat命令用来打印Linux中网络系统的状态信息，可让你得知整个Linux系统的网络情况。

语法
```
netstat  (选项)
```

选项
```
- a或--all：显示所有连线中的Socket；
- A<网络类型>或--<网络类型>：列出该网络类型连线中的相关地址；
- c或--continuous：持续列出网络状态；
- C或--cache：显示路由器配置的快取信息；
- e或--extend：显示网络其他相关信息；
- F或--fib：显示FIB；
- g或--groups：显示多重广播功能群组组员名单；
- h或--help：在线帮助；
- i或--interfaces：显示网络界面信息表单；
- l或--listening：显示监控中的服务器的Socket；
- M或--masquerade：显示伪装的网络连线；
- n或--numeric：直接使用ip地址，而不通过域名服务器；
- N或--netlink或--symbolic：显示网络硬件外围设备的符号连接名称；
- o或--timers：显示计时器；
- p或--programs：显示正在使用Socket的程序识别码和程序名称；
- r或--route：显示Routing Table；
- s或--statistice：显示网络工作信息统计表；
- t或--tcp：显示TCP传输协议的连线状况；
- u或--udp：显示UDP传输协议的连线状况；
- v或--verbose：显示指令执行过程；
- V或--version：显示版本信息；
- w或--raw：显示RAW传输协议的连线状况；
- x或--unix：此参数的效果和指定"-A unix"参数相同；
-- ip或--inet：此参数的效果和指定"-A inet"参数相同。
```
实例

列出所有端口 (包括监听和未监听的)
```
- netstat -a     #列出所有端口
- netstat -at    #列出所有tcp端口
- netstat -au    #列出所有udp端口     
```                        
列出所有处于监听状态的 Sockets
```
- netstat -l        #只显示监听端口
- netstat -lt       #只列出所有监听 tcp 端口
- netstat -lu       #只列出所有监听 udp 端口
- netstat -lx       #只列出所有监听 UNIX 端口
```
在netstat输出中显示 PID 和进程名称
```
- netstat -pt
```
![详细查看](http://man.linuxde.net/netstat)

## expect

```
1. send:用于向进程发送字符
2. expect:从进程中接收字符
3. spawn:启动新的进程
4. interact:允许用户进行交互
```

例如以下的一个例子：

{% highlight ruby %}

\#！/usr/bin/expect
spawn su root
expect "Password:"
send "  \r"
send "cd ./6.828/lab\r"
send "ls\r"
interact
\#文件结束

{% endhighlight %}

如果想要运行这个sh就要使用命令：
/usr/bin/expect xx.sh
这样做的原因是由于spawn是内嵌在expect内的命令如果不这样做就会出现命令找不到的情况。


## >>
```
cat *.txt >> out.txt 将文件夹中所有的txt中的所有行合并到out.txt中    
```
## <
```
./a.out <t.sh
./a.out是一个运行程序
t.sh是命令行的内容
	 > y
	cat < y | sort | uniq | wc > y1
	cat y1
	rm y1
	ls |  sort | uniq | wc
	rm y
也就是运行几行命令
```
## git
```
git add.//将代码更新
git commit -m "commit message"//评论新新增的代码

//从remote端创建一个分支
git clone remote
	e.g. git clone https://pdos.csail.mit.edu/6.828/2017/jos.git
//创建分支（这个我还没有实验）
git branch lab1
//切换分支（我目前的经验就是在一个分支中才能切换分支）
git checkout lab2
//创建并切换分支(我目前的经验也是在一个分支中才能进行创建和切换)
git checkout -b lab3
//在远端分支origin/lab4的基础上创建一个分支并转到这个分支lab4
git checkout -b lab4 origin/lab4
//列出所有的分支信息
git branch -a
```
## df
```
df -h人性化显示硬盘情况
```
## dd

参数注释：

```
1. if=文件名：输入文件名，缺省为标准输入。即指定源文件。< if=input file >

2. of=文件名：输出文件名，缺省为标准输出。即指定目的文件。< of=output file >

3. ibs=bytes：一次读入bytes个字节，即指定一个块大小为bytes个字节。

   obs=bytes：一次输出bytes个字节，即指定一个块大小为bytes个字节。

   bs=bytes：同时设置读入/输出的块大小为bytes个字节。

4. cbs=bytes：一次转换bytes个字节，即指定转换缓冲区大小。

5. skip=blocks：从输入文件开头跳过blocks个块后再开始复制。

6. seek=blocks：从输出文件开头跳过blocks个块后再开始复制。

注意：通常只用当输出文件是磁盘或磁带时才有效，即备份到磁盘或磁带时才有效。

7. count=blocks：仅拷贝blocks个块，块大小等于ibs指定的字节数。

8. conv=conversion：用指定的参数转换文件。

    ascii：转换ebcdic为ascii

    ebcdic：转换ascii为ebcdic

    ibm：转换ascii为alternate ebcdic

    block：把每一行转换为长度为cbs，不足部分用空格填充

    unblock：使每一行的长度都为cbs，不足部分用空格填充

    lcase：把大写字符转换为小写字符

    ucase：把小写字符转换为大写字符

    swab：交换输入的每对字节

    noerror：出错时不停止

    notrunc：不截短输出文件

    sync：将每个输入块填充到ibs个字节，不足部分用空（NUL）字符补齐。
```
dd应用实例

1. 将本地的/dev/hdb整盘备份到/dev/hdd
```
#dd if=/dev/hdb of=/dev/hdd
```

2. 将/dev/hdb全盘数据备份到指定路径的image文件
```
#dd if=/dev/hdb of=/root/image
```

3. 将备份文件恢复到指定盘
```
#dd if=/root/image of=/dev/hdb
```

4. 备份/dev/hdb全盘数据，并利用gzip工具进行压缩，保存到指定路径
```
#dd if=/dev/hdb | gzip > /root/image.gz
```


5. 将压缩的备份文件恢复到指定盘
```
#gzip -dc /root/image.gz | dd of=/dev/hdb
```


6. 备份与恢复MBR
```
备份磁盘开始的512个字节大小的MBR信息到指定文件：
#dd if=/dev/hda of=/root/image count=1 bs=512
   count=1指仅拷贝一个块；bs=512指块大小为512个字节。
恢复：
#dd if=/root/image of=/dev/had
将备份的MBR信息写到磁盘开始部分
```

7. 备份软盘
```
#dd if=/dev/fd0 of=disk.img count=1 bs=1440k (即块大小为1.44M)
```

8. 拷贝内存内容到硬盘
```
#dd if=/dev/mem of=/root/mem.bin bs=1024 (指定块大小为1k)  
```

9. 拷贝光盘内容到指定文件夹，并保存为cd.iso文件
```
#dd if=/dev/cdrom(hdc) of=/root/cd.iso
```
10. 增加swap分区文件大小
```
	第一步：创建一个大小为256M的文件：
	#dd if=/dev/zero of=/swapfile bs=1024 count=262144
	第二步：把这个文件变成swap文件：
	#mkswap /swapfile
	第三步：启用这个swap文件：
	#swapon /swapfile
	第四步：编辑/etc/fstab文件，使在每次开机时自动加载swap文件：
	/swapfile    swap    swap    default   0 0
```
11. 销毁磁盘数据
```
#dd if=/dev/urandom of=/dev/hda1
注意：利用随机的数据填充硬盘，在某些必要的场合可以用来销毁数据。
```
12. 测试硬盘的读写速度
```
#dd if=/dev/zero bs=1024 count=1000000 of=/root/1Gb.file
#dd if=/root/1Gb.file bs=64k | dd of=/dev/null
通过以上两个命令输出的命令执行时间，可以计算出硬盘的读、写速度。
```
13. 确定硬盘的最佳块大小：
```
	#dd if=/dev/zero bs=1024 count=1000000 of=/root/1Gb.file
	#dd if=/dev/zero bs=2048 count=500000 of=/root/1Gb.file
	#dd if=/dev/zero bs=4096 count=250000 of=/root/1Gb.file
	#dd if=/dev/zero bs=8192 count=125000 of=/root/1Gb.file
	通过比较以上命令输出中所显示的命令执行时间，即可确定系统最佳的块大小。
```
14. 修复硬盘：
```
	#dd if=/dev/sda of=/dev/sda 或dd if=/dev/hda of=/dev/hda
	当硬盘较长时间(一年以上)放置不使用后，磁盘上会产生magnetic flux point，当磁头读到这些区域时会遇到困难，
	并可能导致I/O错误。当这种情况影响到硬盘的第一个扇区时，可能导致硬盘报废。
	上边的命令有可能使这些数 据起死回生。并且这个过程是安全、高效的。
```
15. 利用netcat远程备份
```
	#dd if=/dev/hda bs=16065b | netcat < targethost-IP > 1234
	在源主机上执行此命令备份/dev/hda
	#netcat -l -p 1234 | dd of=/dev/hdc bs=16065b
	在目的主机上执行此命令来接收数据并写入/dev/hdc
	#netcat -l -p 1234 | bzip2 > partition.img
	#netcat -l -p 1234 | gzip > partition.img
	以上两条指令是目的主机指令的变化分别采用bzip2、gzip对数据进行压缩，并将备份文件保存在当前目录。
```
16. 将一个大视频文件的第i个字节的值改成0x41（大写字母A的ASCII值）
```
#echo A | dd of=bigfile seek=$i bs=1 count=1 conv=notrunc
```
17. 建立linux虚拟盘，用文件模拟磁盘
```
	在进行linux的实验中，如果没有多余的硬盘来做测试。则可以在linux下使用文件来模拟磁盘，以供测试目的。
	其模拟过程如下所示，摘录自《Oracle数据库核心技术与实务详解－教你如何成为Oracle 10g OCP》一书。
	1)以root用户创建一个ASM磁盘所在的目录。
	# mkdir –p /u01/asmdisks
	2)通过dd命令创建6个400M大小的文件，每个文件代表一块磁盘。
	[root@book u01]# cd asmdisks
	[root@book asmdisks]# dd if=/dev/zero of=asm_disk1 bs=1024k count=400
	[root@book asmdisks]# dd if=/dev/zero of=asm_disk2 bs=1024k count=400
	[root@book asmdisks]# dd if=/dev/zero of=asm_disk3 bs=1024k count=400
	[root@book asmdisks]# dd if=/dev/zero of=asm_disk4 bs=1024k count=400
	[root@book asmdisks]# dd if=/dev/zero of=asm_disk5 bs=1024k count=400
	[root@book asmdisks]# dd if=/dev/zero of=asm_disk6 bs=1024k count=400
	3)将这些文件与裸设备关联。
	[root@book asmdisks]# chmod 777 asm_disk*
	[root@book asmdisks]# losetup /dev/loop1 asm_disk1
	[root@book asmdisks]# losetup /dev/loop2 asm_disk2
	[root@book asmdisks]# losetup /dev/loop3 asm_disk3
	[root@book asmdisks]# losetup /dev/loop4 asm_disk4
	[root@book asmdisks]# losetup /dev/loop5 asm_disk5
	[root@book asmdisks]# losetup /dev/loop6 asm_disk6
	注意：如果要删除通过dd模拟出的虚拟磁盘文件的话，直接删除模拟出的磁盘文件
	（也就是asm_disk1、asm_disk2…asm_disk6）还不够，还必须执行losetup -d /dev/loopN，在这里N从1到6。否则，磁盘文件所占用的磁盘空间不能释放
```

## /dev/null & /dev/zero

/dev/null，外号叫无底洞，你可以向它输出任何数据，它通吃，并且不会撑着！

/dev/zero，是一个输入设备，你可你用它来初始化文件。该设备无穷尽地提供0，可以使用任何你需要的数目——设备提供的要多的多。他可以用于向设备或文件写入字符串0。

/dev/null------它是空设备，也称为位桶（bit bucket）。任何写入它的输出都会被抛弃。如果不想让消息以标准输出显示或写入文件，那么可以将消息重定向到位桶。
```
#if=/dev/zero of=./test.txt bs=1k count=1
#ls –l
total 4
-rw-r--r--    1 oracle   dba          1024 Jul 15 16:56 test.txt
#find / -name access_log  2>/dev/null
```
1. 使用/dev/null 
把/dev/null看作"黑洞"， 它等价于一个只写文件，所有写入它的内容都会永远丢失.，而尝试从它那儿读取内容则什么也读不到。然而， /dev/null对命令行和脚本都非常的有用

禁止标准输出
```
#cat $filename >/dev/null
```
文件内容丢失，而不会输出到标准输出.

禁止标准错误
```
#rm $badname 2>/dev/null
```
这样错误信息[标准错误]就被丢到太平洋去了

禁止标准输出和标准错误的输出
```
#cat $filename 2>/dev/null >/dev/null
```
如果"filename"不存在，将不会有任何错误信息提示；如果"filename"存在， 文件的内容不会打印到标准输出。

因此，上面的代码根本不会输出任何信息。当只想测试命令的退出码而不想有任何输出时非常有用。
```
#cat $filename &>/dev/null
```
这样其实也可以， 由 Baris Cicek 指出

自动清空日志文件的内容

Deleting contents of a file, but preserving the file itself, with all attendant permissions (from Example 2-1 and Example 2-3): 
```
#cat /dev/null > /var/log/messages
#  : > /var/log/messages   有同样的效果， 但不会产生新的进程.（因为:是内建的）
#cat /dev/null > /var/log/wtmp
```
特别适合处理这些由商业Web站点发送的讨厌的"cookies"

隐藏cookie而不再使用
```
#if [ -f ~/.netscape/cookies ]  # 如果存在则删除.
#then
#rm -f ~/.netscape/cookies
#fi
#ln -s /dev/null ~/.netscape/cookies

现在所有的cookies都会丢入黑洞而不会保存在磁盘上了.
```
2. 使用/dev/zero
像/dev/null一样， /dev/zero也是一个伪文件， 但它实际上产生连续不断的null的流（二进制的零流，而不是ASCII型的）。 写入它的输出会丢失不见，而从/dev/zero读出一连串的null也比较困难， 虽然这也能通过od或一个十六进制编辑器来做到。 /dev/zero主要的用处是用来创建一个指定长度用于初始化的空文件，就像临时交换文件

用/dev/zero创建一个交换临时文件
```
	#!/bin/bash
	  # 创建一个交换文件.
	  ROOT_UID=0 # Root 用户的 $UID 是 0.
	  E_WRONG_USER=65 # 不是 root?
	  FILE=/swap
	  BLOCKSIZE=1024
	  MINBLOCKS=40
	  SUCCESS=0
	  # 这个脚本必须用root来运行.
	  if [ "UID"−ne"ROOT_UID" ]
	   then
	   echo; echo "You must be root to run this script."; echo
	   exit $E_WRONG_USER
	  fi
	  blocks={1:-MINBLOCKS} # 如果命令行没有指定，
	  #+ 则设置为默认的40块.
	  # 上面这句等同如：
	  # --------------------------------------------------
	  # if [ -n "$1" ]
	  # then
	  # blocks=$1
	  # else
	  # blocks=$MINBLOCKS
	  # fi
	  # --------------------------------------------------
	 if [ "blocks"−ltMINBLOCKS ]
	 then
	 blocks=$MINBLOCKS # 最少要有 40 个块长.
	 fi
	 echo "Creating swap file of size $blocks blocks (KB)."
	 dd if=/dev/zero of=FILEbs=BLOCKSIZE count=$blocks # 把零写入文件.
	 mkswap FILEblocks # 将此文件建为交换文件（或称交换分区）.
	 swapon $FILE # 激活交换文件.
	 echo "Swap file created and activated."
	 exit $SUCCESS 
	关于 /dev/zero 的另一个应用是为特定的目的而用零去填充一个指定大小的文件， 
	如挂载一个文件系统到环回设备 （loopback device）或"安全地" 删除一个文件
	例子创建ramdisk
	#!/bin/bash
	 # ramdisk.sh
	 # "ramdisk"是系统RAM内存的一段，
	 #+ 它可以被当成是一个文件系统来操作.
	 # 它的优点是存取速度非常快 (包括读和写).
	 # 缺点: 易失性, 当计算机重启或关机时会丢失数据.
	 #+ 会减少系统可用的RAM.
	 # 10 # 那么ramdisk有什么作用呢?
	 # 保存一个较大的数据集在ramdisk, 比如一张表或字典,
	 #+ 这样可以加速数据查询, 因为在内存里查找比在磁盘里查找快得多.
	  E_NON_ROOT_USER=70 # 必须用root来运行.
	  ROOTUSER_NAME=root
	  MOUNTPT=/mnt/ramdisk
	  SIZE=2000 # 2K 个块 (可以合适的做修改)
	  BLOCKSIZE=1024 # 每块有1K (1024 byte) 的大小
	  DEVICE=/dev/ram0 # 第一个 ram 设备
	  username=`id -nu`
	  if [ "username"!="ROOTUSER_NAME" ]
	  then
	    echo "Must be root to run \"`basename $0`\"."
	    exit $E_NON_ROOT_USER
	  fi
	   if [ ! -d "$MOUNTPT" ] # 测试挂载点是否已经存在了,
	  then #+ 如果这个脚本已经运行了好几次了就不会再建这个目录了
	    mkdir $MOUNTPT #+ 因为前面已经建立了.
	  fi
	  dd if=/dev/zero of=DEVICEcount=SIZE bs=$BLOCKSIZE
	   # 把RAM设备的内容用零填充.                                              
	   # 为何需要这么做?
	  mke2fs $DEVICE # 在RAM设备上创建一个ext2文件系统.
	  mount DEVICEMOUNTPT # 挂载设备.
	  chmod 777 $MOUNTPT # 使普通用户也可以存取这个ramdisk.
	  # 但是, 只能由root来缷载它.
	  echo "\"$MOUNTPT\" now available for use."
	 # 现在 ramdisk 即使普通用户也可以用来存取文件了.
	 # 注意, ramdisk是易失的, 所以当计算机系统重启或关机时ramdisk里的内容会消失.
	 # 拷贝所有你想保存文件到一个常规的磁盘目录下.
	 # 重启之后, 运行这个脚本再次建立起一个 ramdisk.
	 # 仅重新加载 /mnt/ramdisk 而没有其他的步骤将不会正确工作.
	 # 如果加以改进, 这个脚本可以放在 /etc/rc.d/rc.local,
	 #+ 以使系统启动时能自动设立一个ramdisk.
	 # 这样很合适速度要求高的数据库服务器.
	 exit 0
```
## linux中显示图片
```
df -h人性化显示硬盘情况
```
## 联合命令用法
```
	ls -l|find *|wc -l
```
## linux中垃圾箱的位置 
```
/home/xuyongkang/.local/share/Trash/files->垃圾箱位置内容
```
## linux中桌面和shell的切换
```
ctrl+alt+F1-F7  ->新开shell，F7是切换回桌面
```
# CMD(windows命令)

## netstat [选项]

```
-a 显示所有socket，包括正在监听的。
-c 每隔1秒就重新显示一遍，直到用户中断它。
-i 显示所有网络接口的信息，格式同“ifconfig -e”。
-n 以网络IP地址代替名称，显示出网络连接情形。
-r 显示核心路由表，格式同“route -e”。
-t 显示TCP协议的连接情况。
-u 显示UDP协议的连接情况。
-v 显示正在进行的工作。
-A 显示任何关联的协议控制块的地址。主要用于调试
-a 显示所有套接字的状态。在一般情况下不显示与服务器进程相关联的套接字
-i 显示自动配置接口的状态。那些在系统初始引导后配置的接口状态不在输出之列
-m 打印网络存储器的使用情况
-n 打印实际地址，而不是对地址的解释或者显示主机，网络名之类的符号
-r 打印路由选择表
-f address -family对于给出名字的地址簇打印统计数字和控制块信息。到目前为止，唯一支持的地址簇是inet
-I interface 只打印给出名字的接口状态
-p protocol-name 只打印给出名字的协议的统计数字和协议控制块信息
-s 打印每个协议的统计数字
-t 在输出显示中用时间信息代替队列长度信息
```

netstat -ano  显示任务占用的端口
```
  协议    本地地址                     外部地址              状态                   PID
  TCP   127.0.0.1:8015        0.0.0.0:0             LISTENING       1456
  TCP   192.168.3.230:139     0.0.0.0:0             LISTENING       4
  TCP   192.168.3.230:1957     220.181.31.225:443     ESTABLISHED     3068
```
tasklist|findstr"3068"   查看PID对应的端口
```
映像名称                       PID 会话名             会话#       内存使用
 ========================= ========================
  tor.exe                     2016 Console                 0     16,064 K
```
taskkill /f /t /im tor.exe  杀死对应的进程

netstat -an中state含义
```
LISTEN：侦听来自远方的TCP端口的连接请求
SYN-SENT：再发送连接请求后等待匹配的连接请求
SYN-RECEIVED：再收到和发送一个连接请求后等待对方对连接请求的确认
ESTABLISHED：代表一个打开的连接
FIN-WAIT-1：等待远程TCP连接中断请求，或先前的连接中断请求的确认
FIN-WAIT-2：从远程TCP等待连接中断请求
CLOSE-WAIT：等待从本地用户发来的连接中断请求
CLOSING：等待远程TCP对连接中断的确认
LAST-ACK：等待原来的发向远程TCP的连接中断请求的确认
TIME-WAIT：等待足够的时间以确保远程TCP接收到连接中断请求的确认
CLOSED：没有任何连接状态
```

## dir(类似于linux中的ls命令)


<h1 align="center"> shell脚本例子<h1>



## 实例1
{% highlight ruby %}
#apply variable
#mkdir fakePairFolder
countNum=0
countNew=1
countMax=40
firstPicName="2.bmp"
secondPicName="3.bmp"
countPairNum=5760
maxPairNum=6000
for firstFolderNum in $(seq 1 $countMax)	
	#find the first folder name
	do
	#       firstFolderName=`find -name "FERET-*" | grep 'FERET-0\{0,2\}'$firstFolderNum'$'`
		firstFolderName=$firstFolderNum
		firstPicSrc=$firstFolderName"/"$firstPicName
		
		let firstFolderNum+=1		
		for secondFolderNum in $(seq $firstFolderNum $countMax)
		#loop the second folder name
			do	
	#			secondFolderName=`find -name "FERET-*" | grep 'FERET-0\{0,2\}'$secondFolderNum'$'`
				secondFolderName=$secondFolderNum
				secondPicSrc=$secondFolderName"/"$secondPicName
				
				
				let countPairNum+=1
				cp $firstPicSrc ./fakePairFolder/"ATFakePair"$countPairNum"_1.bmp"
				echo -n "ATFakePair"$countPairNum"_1.bmp ">>fakePairFolder/fakePairTxt.txt
					
				cp $secondPicSrc ./fakePairFolder/"ATFakePair"$countPairNum"_2.bmp"
				echo  "ATFakePair"$countPairNum"_2.bmp  0">>fakePairFolder/fakePairTxt.txt
				echo $firstPicSrc
				echo $secondPicSrc
				if [ "$countPairNum" -eq "$maxPairNum" ]
					then
					break
				fi
			done
				if [ "$countPairNum" -eq "$maxPairNum" ]
					then
					break
				fi
	done
echo "done"
echo $countPairNum pairs

{% endhighlight %}

## 实例2
{% highlight ruby %}

#apply variable
mkdir realPairFolder
countPairNum=4200
maxPicNum=10
countMax=40

#nameInFind="FERET-*"
#nameInGrep='FERET-0\{0,2\}'
for nowInThisFolderNum in $(seq 1 $countMax)
	do
		#nowInThisFolderName=`find -name "FERET-*"| grep 'FERET-0\{0,2\}'$nowInThisFolderNum'$'`
		nowInThisFolderName=$nowInThisFolderNum
		let maxPicNumForLoopOne=maxPicNum-1	
		for firstPicNum in $(seq 1 $maxPicNumForLoopOne)
			do
				firstPicName=$firstPicNum".bmp"
				firstPicSrc=$nowInThisFolderName"/"$firstPicName
				let startNumForLoopTwo=firstPicNum+1
				for secondPicNum in $(seq $startNumForLoopTwo $maxPicNum)
					do
						secondPicName=$secondPicNum".bmp"
						secondPicSrc=$nowInThisFolderName"/"$secondPicName
						let countPairNum+=1
						cp $firstPicSrc ./realPairFolder/"ATRealPair"$countPairNum"_1.bmp"
						echo -n "ATRealPair"$countPairNum"_1.bmp ">>realPairFolder/realPairTxt.txt
					
						cp $secondPicSrc ./realPairFolder/"ATRealPair"$countPairNum"_2.bmp"
						echo  "ATRealPair"$countPairNum"_2.bmp  1">>realPairFolder/realPairTxt.txt
						
						
						#echo "pair"$countPairNum
						#echo "pair 1:"$firstPicSrc
						#echo "pair 2:"$secondPicSrc
					done
			done
	done
echo "done."
	
{% endhighlight %}

## 实例3

{% highlight ruby %}

for folderName in $(seq 1 40)
	do
		cd $folderName
		for picName in `find -name "*.bmp"`
			do
				let count+=1
				mv $picName $count".bmp"
			done
		let count=0
		cd ..

	done


{% endhighlight %}