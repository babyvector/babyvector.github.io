---
layout: postl
title: linux命令
categories: [linux命令积累]
---

## man 

* 功能：查看命令手册，取自(manual)

* 用法：
	* man name(命令，库函数，系统调用，配置文件)
	* man section name(指定章节号,因为有的时候一个名字可以在不同的章节里出现比如sleep在不同的章节里都会有) 
		* 章节编号：常见
			* 1.命令
			* 2.系统调用
			* 3.库函数
			* 4.配置文件
	* man -k regexp(列出关键字(keyword)与正则表达式regexp匹配的手册像目录)
	* 空格进行翻页
* 列出的内容：
	* 列出基本功能和语法
	* 对于c语言的函数调用，列出头文件和链接函数库
	* 功能说明
	* SEE ALSO：有关的其他项目的名字和章节号(**这是一些重要的交叉信息**)

## netstat

## ln

* 功能：

	建立文件（硬/软连接）或文件夹（软链接）

* 链接的概念：

	linux中的链接分为两种：
	* 硬链接，默认情况下就是硬链接，在linux系统中保存在磁盘分区中的文件都有一个索引节点号。**多个文件名指向同一个索引节点号是存在的这就是硬链接**。这样用户就可以建立硬链接到重要文件，以防止误删。多个目录名对应一个索引节点的时候，删除一个目录名并不影响索引节点和其他目录名的链接。只有删除所有的目录名之后才会删除对应的索引节点。
	* 软连接：可以按照windows系统中的快捷方式来理解（文件或者文件夹都可以建立软连接）

* 简单用法示例：
  * ln filename link_name//通过硬连接将filename文件连接到link_name上；之后任何对二者的任何一个文件操作，另一个也发生相应的改变;但是删除任意一个文件并不会对另一个文件造成影响。
  * ln -s filename soft_link_name//改变任何一个文件内容，另一个文件也会发生相应的改变，但是删除filename的时候，soft_link_name也会失效，反之不会。

* 注意：windows文件夹与VMware上跑的操作系统的文件夹无法建立连接。



## date

* 功能：显示日期

## cal

* 功能：打印日历

* 用法：cal year

## bc

* 功能：计算器(基本计算器功能，支持变量，函数，条件和循环等编程功能<类似C语言的小型编程语言，可以进行任意精度的计算>)

* 精度
	* 缺省精度
		* bc 缺省精度为小数后面**0**位
		* bc -l 缺省精度位小数点后**20**位
	* 可以通过设置scale自行决定精度(小数点位数)
		* scale = 10000

## passwd

* 功能：更改口令
	* 普通用户可以更改自己的口令
	* 超级用户：
		修改口令之前不验证旧的口令
		可修改自己的口令，还可强迫设置其他用户的口令，但是也不能读出其他用户的口令，系统中不存在明码指令
		如果忘记了自己超级用户的口令会比较麻烦

### ntpdate

* 功能：通过NTP协议校对系统时间

* 用法：
	* ntpdate -q 0.pool.ntp.org//来查询时间，普通用户也可以完成

## 正则表达式

* 基本元字符：. * [ \ ^ $
* 其他字符与其自身匹配

## od

* 功能：dump files in octal and other formats;将文件转换为规定进制的数字
* 示例：cat main.c | od -t x1

## iconv

* 功能：中文字符编码的转换
* 示例：
	* iconv -f gbk -t utf8 (from GBK to UTF8)
	* iconv -f gbk -t gbk (from UTF8 to GBK)

## ldd

* 功能：动态链接命令ldd，暂时还不完全
* 使用方法：任何一个运行的程序使用ldd programme就可以看到他所需要的动态链接库。
* 示例：
	文件：main.c
	文件内容：
		#include<stdio.h>
		int main(){
			return 0;
		}
	@1：gcc main.c -o main
	@2: ldd main
	显示结果：
		linux-gate.so.1 => (0xb7f8f000)
		libc.so.6 => /lib/i386-linux-gnu/libc.so.6 (0xb7dbb000)
		/lib/ld-linux.so.2 (0xb7f91000)

## touch
* 功能：生成新的文件；或者更新文件的时间戳

## rsync
* 功能：远程同步remote sync，可以镜像整个目录树；当更新同一个文件，但是时间戳不相同的时候，如果文件太大，rsync只对文件发生变化的部分进行更新。

## copy
* copy -u 
-u选项进行增量拷贝(update)，就是两个相同的文件夹中有相同的文件，但是时间戳是不相同的，所以在进行增量拷贝的时候只将最新的时间戳的文件拷贝过去，进行更新。

## xargs
* 功能：将标准输入构造成为命令的命令行参数。当需要进行处理的文件数目过多的话，xargs会自行启动多线程。
**注意：这个叫做批处理**,当在命令中使用这个参数的时候会极大的加快命令的运行速度，因为这个时将标准输入进行成批的操作，极大的加快速度。

## time
* 功能：显示一个命令执行过程所用的时间
* 示例： time find ./ -name '*.c'
	如果运行的命令是一个组合的功能，就要time （find ... -exec ...）

## tar
* 功能：进行文件的**归档**，和抽取（一开始是用在磁带文件上的）

* -c Create创建新磁带。从头开始写，以前存于磁带上的数据会被覆盖掉
* -t Table列表。磁带上的文件名列表，当不指定文件名时，将列出所有文件
* -x eXTract抽取。从磁带中抽取指定的文件。当不指定文件名时，抽取所有的文件。 
* v Verbose冗长。每处理一个文件，就打印出文件名，并在该名前冠以功能字母
* **f**这个字母非常的重要，用来指定设备文件名
* z 采用gzip压缩算法
* j 采用bzip2压缩算法

用例：
	tar cvf /dev/rct0 .
	将当前目录树备份到设备/dev/rct0中，圆点表示的目录是当前目录
	tar tvf /dev/rct0
	查看磁带设备/dev/rct0上的文件目录
	tar xvf /dev/rct0
	将磁带设备/dev/rct0上的文件恢复到文件系统中

## gzip

* 功能：进行压缩操作，压缩速度比较快，但是效率比较低
## gunzip

## bzip2

* 功能：进行压缩，压缩速度比较慢但是，压缩效率比较高
## bunzip2

## export
* 功能：可以设置永久的环境变量
  例如：export SERVER=1.2.34.4

## env
* 功能：可以查看本机中的环境变量

## dd

**注意：**这个命令是一种特殊风格的命令
* 功能：磁盘的拷贝功能
* 示例：
	dd if=/dev/urandom of=test.dat bs=1024 count=512
	用dd命令，生成512KB测试数据文件test.dat
	命令行参数中：if,of,bs,count分别指定输入文件，输出文件，块大小(block size)，以及块计数。

## /dev

* 作用：这个东西是一个设备驱动
* /dev/null 这个是一个文件驱动
* /dev/urandom 这个相当于一个无限的产生随机数的堆

## gcc
* 示例：

		gcc -O0 -Wall -g -masm=intel -Wa,-ahl -c main_old.c
		gcc -O0 -Wall -g 
		-g生成调试信息
		-masm=intel 生成的汇编格式文件按照intel方式进行输出
		-Wa, -ahl 
			-Wa,说明后面这些命令都是给汇编文件说的a高级语言,h汇编语言l将这些文件进行列表

		命令总体：编译c语言源程序文件mytest.c并生成c程序与汇编代码对比
## 命令风格
* 风格1：dd 之类的功能
* 风格2：find gcc之类
		特点:以减号打头的一个**由多个字符构成的单词**用作选项
		find src -name '*.c' -type f -exec dos2unix --keepdate {} \;
		将所有拓展名.c的普通文件由windows文本格式转换为Linux格式

		gcc -O0 -Wall -g -masm=intel -Wa,-ahl -c main_old.c
		gcc -O0 -Wall -g 
		-g生成调试信息
		-masm=intel 生成的汇编格式文件按照intel方式进行输出
		-Wa, -ahl 
			-Wa,说明后面这些命令都是给汇编文件说的a高级语言,h汇编语言l将这些文件进行列表

		命令总体：编译c语言源程序文件mytest.c并生成c程序与汇编代码对比
* 风格3：ls grep 当今比较流行的格式
		特点：长选项与短选项，有的选项同时有两种格式，也有的选项仅有长格式或仅有短格式
		ls --classify --all --size --human-readable --width=80 /home/jiang 长选项的方式
		ls -Fashw80 /home/jiang 多个选项挤在一起
		ls -F -a -s -h -w 80 /home/jiang 多个选项分开
		ls -F -w80 /home/jiang -has 可以把选项放到后面

## mkfs

* 功能：执行此命令的时候是对文件系统进行初始化。

## df

* 功能：df命令读出部分信息
* 示例：df -i和df



## gcc

* gcc主要包括:
	* cpp（预处理器）
	* gcc(c编译器),g++(c++编译器)等编译器
		* binutils等二进制工具
			* as(汇编器)
			* ld（链接器）
			* ...

* 在linux下面编译的过程：
	* 源文件 -> 预处理 -> 编译 -> 汇编 -> 链接 -> 可执行
	* cpp -o hello.i hello.c  预处理
	* ccl -o hello.s hello.i  编译
	* as -o hello.o hello.s	  汇编
	* ld -o hello hello.o     链接

* gcc命令格式：gcc [选项]<文件名>
	* -o filename：指定输出文件为filename.该选项gcc产生什么输出，无论是可执行文件，目标文件，汇编文件还是预处理后的c代码
	* 如果没有使用-o选项，默认的输出结果是：可执行文件为"a.out"，编译后产生的目标文件是"sourcename.o"，汇编文件是"sourcename.s",而预处理后的C源代码送往标准输出。
	* 对于源代码main.c，可以通过如下命令编译成最终可执行文件（默认包含了预处理、编译、汇编及链接四个阶段）
		
* gcc main.c -o main

gcc -c 表示只是进行编译，而不进行链接
gcc -o 表示进行编译之后要进行链接

## ls

* 功能说明
  
	ls=list  -a -l -h -d -i

* 参数说明
  * ls -a(all)显示所有文件包括隐藏文件（前面加.表示，所以隐藏文件时候前面加点)
  * ls -l(long长格式显示)显示较为详细的文件信息
  * ls -h(human)更人性化的显示文件大小(ls -lh=ls -hl=显示更为详细的文件信息）
  * ls -d指定目录查看
  * ls -i查看文件的i节点就像省份证号一样（查看文件的文件描述符，每两个不同的文件描述符不相同）
       一般可以组合使用 ls -asSh  为显示文件的大小 S是对文件进行大小排序

* 例子：

```
ls -lR  ->R代表子目录
ls -l|find *|wc -l
```

## su

	su -l lilei 用lilei进行登陆
## nl
	nl  使用方式跟cat一样只是更专业
	    -b : 指定添加行号的方式，主要有两种：
	        -b a:表示无论是否为空行，同样列出行号("cat -n"就是这种方式)
	        -b t:只列出非空行的编号并列出（默认为这种方式）
	    -n : 设置行号的样式，主要有三种：
	        -n ln:在行号字段最左端显示
	        -n rn:在行号字段最右边显示，且不加 0
	        -n rz:在行号字段最右边显示，且加 0
	    -w : 行号字段占用的位数(默认为 6 位)
## mv

```

移动或者重命名文件

mv 源目录文件  目的目录
mv 旧的文件名  新的文件名

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
## fg
```
	jobs是和fg配合使用的
	$ fg %find
	find/-name README -print > logfile
	注意,显示的命令行末尾没有&符号.下面的命令能产生同样的效果:
	$ fg %1
```
## rm

```
删除一个文件或者目录
rm test 删除test文件
rm -f test 强制删除文件，忽视权限限制
rm -r family 删除一个目录
//删除除了某个文件之外的所有文件
root@ubuntu:/home/xuyongkang/6.828/lab_github# shopt -s extglob		//首先打开extglob模式 
root@ubuntu:/home/xuyongkang/6.828/lab_github# rm -rf !(old_jos)
rm -rf !(old_jos|new_jos)//删除除了old_jos和new_jos之外的文件

```
## bg
```
	它是把前台进程换到后台执行,其使用格式是:
	bg [job...]
	其中,job还是一个或多个进程的PID,命令名称或作业号,在参数前要带%号.例如,在cc(C编译命令)命令执行过程中,按
	Z键,使作业挂起.然后键入以下命令:
	$ bg %cc
	该挂起的作业在后台重新开始运行.........  <<<这个非常的重要能够经常的用到>>>

	ctrl+Z将正在前台运行的程序暂停
	jobs 将任务列出来
	bg %index num将任务放入后台运行
```

## cat

* 命令的功能：cat进行文件的读取,文件查看
* 例子：
  * cat filename      //其实实际上cat有很多很多的用法
  * cat -n filename1 filename2 > filename3 	//合并两个文件到filename3,并在filename3中显示行号
  * cat -n passwd  //cat 参数  文件  -n表示列出行号
  * cat > main.c	//使用键盘在终端中创建一个新文件
## wc
查看文件中的行数或者字数
```
用法：wc
	[选项]...[文件]...
	-c打印字节数
	-m打印字符数
	-l打印换行数
	-L显示最长行的长度
	-w统计单词个数
	eg.wc file1 file2

用途：统计文件中的单词数量（Word Count）等信息
格式:wc [选项]…  目标文件…
	显示的是  行数量  字数量  字节数

```

## dir
(类似于linux中的ls命令)

## jobs
```
jobs -l显示所有的后台运行进程
$jobs
	[2] + running tar tv3 * &
	[1] - running find/ -name README -print > logfile&
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
	
	//关于git clone、git pull、git push的用法
	1、git clone 与 git pull 相同点
	相同点：都是从远程服务器拉取代码到本地
	
	2、git clone 与 git pull 不同点
	
	git clone
	是在本地没有版本库的时候，从远程服务器克隆整个版本库到本地，是一个本地从无到有的过程。
	
	git pull
	在本地有版本库的情况下，从远程库获取最新commit 数据（如果有的话），并merge（合并）到本地。
	
	git pull = git fetch + git merge
	
	3、使用场景
	通常情况下，远程操作的第一步，是使用git clone从远程主机克隆一个版本库到本地。
	
	本地修改代码后，每次从本地仓库push到远程仓库之前都要先进行git pull操作，保证push到远程仓库时没有版本冲突。
	
	原文：https://blog.csdn.net/lijing742180/article/details/84636977 
	
	
	通常的用法就是
	先使用git add filenames 将所有更改的文件保存到本地或者云端
	之后使用git commit -m "your descriptions" 记录这次的更改
	之后再git push就会将所有更改的代码提交到相应的网站上

实例：将相关代码提交到github网站上
>https://blog.csdn.net/qq_33877149/article/details/79672918

```
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
11. 保存文件到特定的路径
```
	: saveas 文件路径
```
12. 终端没有正常关闭后恢复文件
```
	vi -r filename

	//不过要小心使用，而且使用之前最好先把要恢复的文件备份一下
	//因为恢复一部分文件后，可能会造成更多内容的丢失

```

## who
who 进行用户查看;列出当前已经登陆入系统的用户

     -a  能打印的全都打印  
     -d  打印死掉的进程		
     -m  
     -q  打印当前登录的用户数及用户名
     -u  打印当前登录用户的登陆信息
     -r  打印运行等级
## ssh

SSH是**Secure Shell**的缩写，由IETF的网络小组(Network Working Group)所制定。利用SSH协议可以有效防止远程管理过程中信息泄漏问题，简单说，SSH是一种**网络协议**，用于计算机之间的**加密登录**

公钥登录：

公钥登录，很多时候说public key认证，公钥登录的原理：

首先用户将自己的公钥存储在需要登录的远程机器上面；

登录的时候，远程主机会向用户发送一段随机字符串，接着**用户使用自己的私钥加密字符串**，并发送给主机。最后，**远程主机使用存储的公钥进行解密**，若解密成功，则说明用户可信，准许登录，不再提示输入密码。

备注：

1. 服务端会将客户端发的公钥写入到~/.ssh/authorized_keys文件末尾。
2. 公钥和私钥在客户端（登录端）生成

口令登录：

口令登录，即登录的时候需要输入登录密码。

1.客户端向服务器发出请求

2.服务器将自己的公钥返回给客户端;

3.客户端用服务器的公钥加密登录信息, 再将信息发送给服务器;

4.服务器接收到客户端传送的登录信息, 用自己的私钥解码, 如果结果正确, 则同意登录, 建立起连接。


原文：https://blog.csdn.net/hsd2012/article/details/79469747 

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
## cmp
```
比较任意两个类型的文件，把结果输出到标准输出上
默认：文件相同不输出
	 不同文件输出不同
参数：
	-c显示不同的信息
	-l列出所有的不同信息
	-s错误信息不提示
```
## diff
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

格式：diff [选项]… 文件1 文件2
常见命令选项
	-c 显示全部内文，并标出不同之处
-b不检查空格字符的不同
-r比较子目录中的文件
-s若没有发现任何差异，仍然显示信息
		diff  test1.c  test/test.c 命令使用的例子
		diff –u test1.c  test/test.c显示的信息中---表示旧文件，+++表示新文件；通常是用来打补丁使用的（用来生成补丁文件，可以用patch命令来打补丁）显示的文本中带有+的表示是要增加的，如果是减的话表示是要减去的。
		打补丁的例子
		diff -uN test/test.c test1.c>test.c.patch生成补丁文件
patch -p0 test1.c test.c.patch利用补丁命令进行打补丁

```
## sudo

sudo 只有拥有root 权限的用户才能使用

     sudo adduser  创建一个新用户
     sudo adduser lilei 创建一个lilei的新用户
     sudo usermod   为用户添加用户组
     sudo usermtouchod -G sudo lilei  将lilei加入sudo组
     sudo chown   变更文件所有者
     sudo chown shiyanlou iphone6 将iphone6文件变更给shiyanlou
     sudo deluser  删除一个用户
     sudo deluser lilei  删除一个名叫lilei的用户

## touch
	 touch  命令用于创建一个空白文件
	 touch test 创建一个test空白文件
     touch zhongguo.txt
## groups
		groups  进行用户组查询
		groups shiyanlou


## more
more  阅读器
   more 文件名
   enter键向下翻一行
   space翻一整篇
   h  help
   q  quit
## less

Less命令
用途：跟more类似可以进行前后翻页而more只能往下进行翻页
	 Less test.txt 显示之后需要输入q退出。

## file
	这个功能用来显示文件类型，例如echo就是一个二进制类型文件
	
## chmod

chmod 对文件的权限进行修改

      chomd 700 iphone6 是指将iphone6文件的权限修改为-rwx------



## echo

	 echo 打印字符
	 echo hello world >iphone6    向iphone6文件中写入hello world

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

## whereis-which-find-locate

## whereis

简单快捷
         whereis who

## locate

快而全可以用来查找指定目录下的不同类型的文件
	如locate /etc/sh 用来查找/etc下所有的sh开头的文件
	locate /usr/share/\*jpg查找/usr/share/下的所有jpg文件

## which

	which 小而精通常是用来确定是否安装了某个软件
	如 which man

## rename

```
rename 用perl正则表达式来作为一个参数进行重命名
        touch file{1..5}.txt
       # 使用通配符批量创建 5 个文件
       $ touch file{1..5}.txt

       # 批量将这 5 个后缀为 .txt 的文本文件重命名为以 .c 为后缀的文件
       $ rename 's/\.txt/\.c/' *.txt

       # 批量将这 5 个文件，文件名改为大写
       $ rename 'y/a-z/A-Z/' *.c
```

## head
```
	输出文件的前3000行
	head -n 3000 test.txt >temp.txt
```
## tail

```
与head仿照相似即可的
```

## eval

执行字符串中内容
```
    eval  使用方法
	command1='firstPicName="06_"$firstDirCount".tif"'
	eval command1;
	这个时候相当于执行了一句firstPicName="06_"$firstDirCount".tif"
```
## mkdir

```
	1.嵌套建立文件夹(-p如果不存在父目录创建父级目录)
	mkdir -p /home/xuyongkang/test   ->直接建立嵌套的文件夹
	mkdir test1 test2
```




## and符号
```
	command&  将命令放入后台运行
```

## export

    export的作用是增加系统的环境变量
	CPLUS_LIBRARY_PATH -> c++编译的环境变量的路径
	LD_LIBRARY_PATH ->动态链接库的环境变量的路径
	例子：
	export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/root/data

## top-htop-atop

```
    top动态显示进程的运行状况
    htop动态显示内存和CPU的占用情况
    atop动态显示内存CPU和GPU的使用情况
```
## shuf

抽取随机数
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
## grep

grep 这个命令有一个特点，因为当查找的文件是只有一个的时候，他列出信息的时候只是列出行号，而不会显示出文件文件名。因此可以在查找时并上/dev/null这样就能够显示出文件了。

* 用途：在文件中查找并显示包含制定字符串的行
* 格式：grep  [选项]…. 查找条件 目标文件
* 常用命令选项
	* -i:查找时忽略大小写
	* -v：反转查找，输出与查找条件不相符的行
	* 查找条件设置，正则表达式
		* 要查找的字符串以双引号括起来
		* “^……”表示以……开头，”……$”表示以……结尾
		* “^$”表示空行
	* -H , 意思是：with-filename
	* -h , 意思是: no-filename
* 使用实例：
	* grep –n  “/dev” tst(-n是显示行号的意思，整个意思是在tst文件中搜索带有/dev字符的文件
	* 以下是一个例子grep -n "iostream" /home/xuyongkang/Desktop/test.c)<这是grep配合字符串搜索使用>

	* grep –n “sdc[0-6]” tst  <这是grep配合正则表达式使用>
	* Eg：ls –al | grep ^d(显示以d打头的目录，grep进行筛选)


## find

使用方法

```  
	find [path] [option] [expression]
	@1:首先确定查找范围
	@2:满足条件的目录或者文件的条件
	@3:满足要求之后执行的动作
```

例子

```
	例子1：
	find ./total/ -name "*.tif" -exec cp {} /amyfile/train/ \;
	这里加入了\;这样才能正常运行否则就不能正常的运行。
	这样做能构保证当复制大量的文件的时候不会出现参数列表过长出现无法复制的情况，
	因为使用普通的mv和cp的时候因为参数列表没法进行复制。
	例子2：
	find ver1.d ver2.d -name '*.c' -print
	1. 范围:当前目录的子目录ver1.d和ver2.d
	2. 条件：与名字*.c匹配。注：*.c应当用引号括起来
	3. 动作：把查找到的文件的路径打印出来
```
更详细的功能
	
* ()表示与但是要加上反斜线进行转义 \(\)反斜线必须要加，因为shell会对圆括号进行特殊的处理.
	一般情况下 find ! -type d -links +2 -print这种情况下也是与

* -o 表示或
* ！表示非

* -mtime 
	表示最近的修改的时间
	find / -type f -mtime -10 -print
	从根目录开始检索最近10天之内曾经修改过的普通磁盘文件（目录不算）
* -size
	表示体积+-表示大于和小于
	find ~ -size +100k \(-name core -o -name '*.tmp'\) -print
	找文件大于100k名字叫做core 或者能够匹配'*.tmp',然后打印出来
* （0）限定搜索的层数

```
find ./ -maxdepth 1 搜索本地目录，限定搜索的层数最大层数为测层
```

* （1）反向查找

```
除了查找满足条件的文件之外，我们还可以查找不满足条件的所有文件。当我们知道要在查找中排除哪些文件时，这个选项就能发挥作用了。

find ./test -not -name "*.php"
```

* （2）结合多个查找条件

```		
我们可以同时使用多个查找条件来指定文件名并排除某些文件。
		
find ./test -name 'abc*' ! -name '*.php'
```  
 	
* （3）只查找文件或目录

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
	
* （4）查找属于特定用户的文件

```
查找当前目录下，属于 bob 的文件。
	$ find . -user bob
查找属于特定用户组的文件
	# find /var/www -group developer
```
	
* （5）查找过去的第 N 天被修改过的文件

```
# find / -mtime 50
```
* （6） 查找过去的 N 分钟内状态发生改变的文件

```
$ find /home/bob -cmin -60
```
	
* （7）基于文件大小的查找

```
查找指定大小的文件(-号代表小于的意思)
	$ find / -size 50M
查找大小在一定范围内的文件(+号代表大于的意思)
	$ find / -size +50M -size -100M
```

* （8）高级操作

```
	命令解释:
	
	find -size +20M -print0|xargs -0 du -h | sort -nr

	查看当前文件下文件大小大于20M的文件，并显示大小，之后排序显式。
```

* （9）结合-exec(-exec \;二者要配对进行使用，并且可以使用很多对)

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

```
看一个stackflow上的讲解：
这个是别人的提问：
spawn ssh -oStrictHostKeyChecking=no -oCheckHostIP=no usr@$myhost.example.com'<br>
expect "password"<br>
send "$PWD\n" <br>
EOD
这个是解释：
It is part of an expect script:

spawn opens a new process

expect waits for the previous spawned process to output the "expected" string (password in this case)

send writes to the spawned process stdin

EOD has no meaning in expect, perhaps it is part of another script?
```

## ping
```
	ping -c 10 -i 1 www.baidu.com
		64 bytes from 111.13.100.92: icmp_seq=1 ttl=53 time=16.6 ms
		64 bytes from 111.13.100.92: icmp_seq=2 ttl=53 time=15.5 ms
		64 bytes from 111.13.100.92: icmp_seq=3 ttl=53 time=15.5 ms

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



## bash

bash配置文件->环境变量常用变量如下：
1. PATH  决定了shell将到那些目录中寻找命令或程序
2. HOME  当前用户的主目录
3. HISTSIZE  历史记录数
4. LOGNAME  当前用户的登录名
5. HOSTNAME  指主机的名称
6. SHELL  当前用户的shell类型
7. LANG  语言相关的环境变量，多语言可以修改此环境变量
8. MAIL  当前用户的邮件存放目录




## 环境变量

* 环境变量一般情况下存放的文件夹
  * /etc/profile
  * bash与bash profile
  * 在当前用户文件夹下面建一个./bash/profile
  * 改/etc/profile中的文件
    * vi /etc/profile
    * 然后往文件中加入export JAVA_HOME=/usr/local/jdk1.7.0.05
					
					export PATH=$PATH:$JAVA_HOME/bin
    * 然后执行source /etc/profile让其生效
    * 然后写java --version就可以看到有没有生效
    * 如果还不能及时生效请重启

* set

	显示当前 Shell 所有环境变量，包括其内建环境变量（与 Shell 外观等相关），用户自定义变量及导出的环境变量
* env

	显示与当前用户相关的环境变量，还可以让命令在指定环境中运行
* export

	显示从 Shell 中导出成环境变量的变量，也能通过它将自定义变量导出为环境变量 //存在时间应该是最长的

* 相关测试

	 $ temp=shiyanlou
	 $ export temp_env=shiyanlou
	 $ env|sort>env.txt
	 $ export|sort>export.txt
	 $ set|sort>set.txt
	 上述操作将命令输出通过管道|使用sort命令排序，再重定向到对象文本文件中。



## 正则表达式

			touch file{1..5}.txt
			# 使用通配符批量创建 5 个文件
			$ touch file{1..5}.txt

			# 批量将这 5 个后缀为 .txt 的文本文件重命名为以 .c 为后缀的文件
			$ rename 's/\.txt/\.c/' *.txt

			# 批量将这 5 个文件，文件名改为大写
			$ rename 'y/a-z/A-Z/' *.c

```
	^：表示匹配开始
	$:表示匹配结束
	[A-Za-z0-9]匹配
	0\{0,2\}:表示有0到2个0
>http://www.cnblogs.com/Quincy/archive/2012/03/26/2418485.html
```

## shell脚本
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
> http://www.cnblogs.com/aaronLinux/p/7074725.html（条理清晰）






## 文件的打包与压缩

文件的打包与解压一般包括三个命令zip rar tar
### 压缩命令
1. zip -r(进行递归压缩) -9（压缩都为9） -q（安静解压模式）/*-e（加密压缩）*/ -o（文件名） shiyanlou.zip /home/shiyanlou

2. tar
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
tar对文件进行打包
 tar -c(表明创建一个tar文件)f（表示文件名） shiyanlou.tar ~
 tar -czf shiyanlou.tar.gz ~创建文件包并以gz的形式进行压缩
 tar -xzf shiyanlou.tar.gz 将创建的gz文件进行解压
 
### 解压命令

  unzip shiyanlou.zip  解压到当前文件夹
  unzip -q shiyanlou.zip -d ziptest 解压到指定文件夹
  unrar e shiyanlou.rar tmp/  将shiyanlou文件解压到tmp/目录下






## grep-sed-awk  
```
	其中文本文件非常重要，因为unix提供 很多了实用程序（utility），这些程序功能相对独立，
	但可以通过管道来实现进程间通信（IPC），这样松散的耦合可以完成复杂的操作。
	grep搜索
	sed 修改和编辑文本文件中某些行
	awk 是访问文本文件，操纵文本文件中某些数据

	awk命令的使用示例：
		ps -ef|grep "qemu"|awk '{print "kill -9 " $2}'|sh
		这个命令作用是批量杀死进程|sh这句语句才能最终起效果
	
```
## du文件大小
```
	du 是直接查看各个目录的大小，而不是从硬盘中获得信息。
	所以有可能会遇到du df命令产生不同结果的情况。
```


## 查看磁盘情况
```
	df -hl(直接查询磁盘中的文件的大小信息)
	df -m是以M的形式显示容量b k m g t
	   -h是以人性化形式显示
```

## xargs 和 find

```
	xargs:将linux命令产生的输出：文件列表，字符串列表等传递给后面相应的命令。
	

	命令解释：
	
	file -Lz * | grep ASCII | cut -d":" -f1 | xargs ls -ltr
	让我们来剖析这个命令字符串。第一个，file -Lz *，用于查找是符号链接或经过压缩的文件。他将输出传递给下一个命令 grep ASCII，该命令在其中搜索 "ASCII" 字符串并产生如下所示的输出：
	
	alert_DBA102.log:        ASCII English text
	alert_DBA102.log.Z:      ASCII text (compress’d data 16 bits)
	dba102_asmb_12307.trc.Z: ASCII English text (compress’d data 16 bits)
	dba102_asmb_20653.trc.Z: ASCII English text (compress’d data 16 bits)
	由于我们只对文件名感兴趣，因此我们应用下一个命令 cut -d":" -f1，仅显示第一个字段：
	
	alert_DBA102.log
	alert_DBA102.log.Z
	dba102_asmb_12307.trc.Z
	dba102_asmb_20653.trc.Z
	目前，我们希望使用 ls -l 命令，将上述列表作为参数进行传递，一次传递一个。xargs 命令允许你这样做。最后一部分，xargs ls -ltr，用于接收输出并对其执行 ls -ltr 命令，如下所示：
	ls -ltr alert_DBA102.log
	ls -ltr alert_DBA102.log.Z
	ls -ltr dba102_asmb_12307.trc.Z
	ls -ltr dba102_asmb_20653.trc.Z
	因此，xargs 本身虽然没有多大用处，但在和其他命令相结合时，他的功能非常强大。

	命令解释:
	
	find -size +20M -print0|xargs -0 du -h | sort -nr

	查看当前文件下文件大小大于20M的文件，并显示大小，之后排序显式。

```
转自:

>https://www.cnblogs.com/Andy-Lv/p/5312597.html


## 位左移符号
```
cat *.txt >> out.txt 将文件夹中所有的txt中的所有行合并到out.txt中    
```
## 小于号
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

## df
```
df -h人性化显示硬盘情况
```
## dd

* 参数注释：
  1. **if=文件名：输入文件名，缺省为标准输入。即指定源文件。**< if=input file >

  2. **of=文件名：输出文件名，缺省为标准输出。即指定目的文件。**< of=output file >

  3. ibs=bytes：一次读入bytes个字节，即指定一个块大小为bytes个字节。

  	 obs=bytes：一次输出bytes个字节，即指定一个块大小为bytes个字节。

   	 **bs=bytes：同时设置读入/输出的块大小为bytes个字节。**

  4. cbs=bytes：一次转换bytes个字节，即指定转换缓冲区大小。

  5. skip=blocks：从输入文件开头跳过blocks个块后再开始复制。

  6. seek=blocks：从输出文件开头跳过blocks个块后再开始复制。

	**注意：通常只用当输出文件是磁盘或磁带时才有效，即备份到磁盘或磁带时才有效**。

  7. **count=blocks：仅拷贝blocks个块，块大小等于ibs指定的字节数。**

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

	**注意：关于dd命令最常用的几个参数是:if of bs count.**

* dd应用实例
  1. 将本地的/dev/hdb整盘备份到/dev/hdd

		```
		#dd if=/dev/hdb of=/dev/hdd
		```
  
   2. 将/dev/hdb全盘数据备份到指定路径的image文件

		```
		#dd if=/dev/hdb of=/root/image
		```

   1. 将备份文件恢复到指定盘


		```
		#dd if=/root/image of=/dev/hdb
		```

   1. 备份/dev/hdb全盘数据，并利用gzip工具进行压缩，保存到指定路径

		```
		#dd if=/dev/hdb | gzip > /root/image.gz
		```

   1. 将压缩的备份文件恢复到指定盘

		```
		#gzip -dc /root/image.gz | dd of=/dev/hdb
		```

   1. 备份与恢复MBR
      1. 备份磁盘开始的512个字节大小的MBR信息到指定文件：
		
			#dd if=/dev/hda of=/root/image count=1 bs=512
		
			count=1指仅拷贝一个块；bs=512指块大小为512个字节。
		
	  2. 恢复：
		
			#dd if=/root/image of=/dev/had
			
			将备份的MBR信息写到磁盘开始部分
		

   1. 备份软盘

		```
		#dd if=/dev/fd0 of=disk.img count=1 bs=1440k (即块大小为1.44M)
		```

   1. 拷贝内存内容到硬盘

		```
		#dd if=/dev/mem of=/root/mem.bin bs=1024 (指定块大小为1k)  
		```

  1. 拷贝光盘内容到指定文件夹，并保存为cd.iso文件

		```
		#dd if=/dev/cdrom(hdc) of=/root/cd.iso
		```

  1.  增加swap分区文件大小

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

  1.  销毁磁盘数据

		```
		#dd if=/dev/urandom of=/dev/hda1
		注意：利用随机的数据填充硬盘，在某些必要的场合可以用来销毁数据。
		```

  1.  测试硬盘的读写速度

		```
		#dd if=/dev/zero bs=1024 count=1000000 of=/root/1Gb.file
		#dd if=/root/1Gb.file bs=64k | dd of=/dev/null
		通过以上两个命令输出的命令执行时间，可以计算出硬盘的读、写速度。
		```

  1.  确定硬盘的最佳块大小：

		```
			#dd if=/dev/zero bs=1024 count=1000000 of=/root/1Gb.file
			#dd if=/dev/zero bs=2048 count=500000 of=/root/1Gb.file
			#dd if=/dev/zero bs=4096 count=250000 of=/root/1Gb.file
			#dd if=/dev/zero bs=8192 count=125000 of=/root/1Gb.file
			通过比较以上命令输出中所显示的命令执行时间，即可确定系统最佳的块大小。
		```

  1.  修复硬盘：

		```
			#dd if=/dev/sda of=/dev/sda 或dd if=/dev/hda of=/dev/hda
			当硬盘较长时间(一年以上)放置不使用后，磁盘上会产生magnetic flux point，当磁头读到这些区域时会遇到困难，
			并可能导致I/O错误。当这种情况影响到硬盘的第一个扇区时，可能导致硬盘报废。
			上边的命令有可能使这些数 据起死回生。并且这个过程是安全、高效的。
		```

  1.  利用net远程备份

		```
			#dd if=/dev/hda bs=16065b | netcat < targethost-IP > 1234
			在源主机上执行此命令备份/dev/hda
			#netcat -l -p 1234 | dd of=/dev/hdc bs=16065b
			在目的主机上执行此命令来接收数据并写入/dev/hdc
			#netcat -l -p 1234 | bzip2 > partition.img
			#netcat -l -p 1234 | gzip > partition.img
			以上两条指令是目的主机指令的变化分别采用bzip2、gzip对数据进行压缩，并将备份文件保存在当前目录。
		```

  1.  将一个大视频文件的第i个字节的值改成0x41（大写字母A的ASCII值）

		```
		#echo A | dd of=bigfile seek=$i bs=1 count=1 conv=notrunc
		```

  1.  建立linux虚拟盘，用文件模拟磁盘

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

## dev-null和dev-zero

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
## linux文件分析


1. /bin /usr/bin  /usr/local/bin   
	
	都是放置用户可执行二进制文件。

2. /boot 

	主要是放置liunx系统启动时用到的文件。

2. /dev   

	文件夹内主要是西东外设与存储有关的一些相关文件。

3. /etc  

	放置设置文件。例如用户帐号密码文件，各种服务文件。
	
	/etc/sysconfig/目录包括了在LINUX下各种系统配置文件
	
	/etc/xinetd.d/这个是超级守护程序管理的各项服务的设置文件目录。
	
	/etc/x11这与x window的设置有关。
	
	/etc/gateways 设定路由器
	
	/etc/host.conf 文件说明用户的系统如何查询节点名
	
	/etc/hosts 设定用户自已的IP与名字的对应表
	
	/etc/hosts.equiv 设置远端机不用密码
	
	/etc/init.d/多有服务的默认启动脚本都放在这里。
	
	/etc/named.boot 设定本机为名字服务器的配置文件
	
	/etc/resolv.conf    设置DNS 
	
	/etc/fstab    记录开机要mount的文件系统
	/etc/inittab 设定系统启动时init进程将把系统设置成什么样的runlevel
	
	/etc/issue 记录用户登录前显示的信息
	
	/etc/group 设定用户的组名与相关信息
	
	/etc/passwd 帐号信息
	
	/etc/shadow 密码信息
	
	/etc/sudoers 可以sudo命令的配置文件
	
	/etc/securetty 设定哪些终端可以让root登录
	
	/etc/login.defs 所有用户登录时的缺省配置
	
	/etc/exports 设定NFS系统用的
	
	/etc/modprobe.conf   内核模块额外参数设定
4. /home 

	系统默认的的用户目录。

5. /lib  /usr/lib  /usr/local/lib  

	系统使用的函数目录

6. /lost+found 

	系统出现异常，产生错误时，会将一些遗失的片段存放于此目录下。

7. /mnt /media 

	这是软盘与光盘的默认挂在点。

8. /opt 

	这是给主机额外安装软件的目录。

9. /root 

	系统管理员的家目录。

10. /sbin /usr/sbin /usr/local/sbin 

	放置一些西东管理员才会用到的命令。

11. /srv  

	一些服务启动后，这些服务需要访问的数据目录。例如：www服务器需要的网页数据就放在/srv/www中。

12. /tmp 

	这是一般用户或者正在执行的临时文件存放的地方，任何人都可以访问，需要定期清理。

13. /usr 

	包括系统的主要程序、图形界面所需要的文件、额外的函数库、本季自行安装的文件，以及共享的目录和文件。

	/usr/includec/c++等程序的文件头与包含文件。
	
	/usr/lib各种应用软件的函数文件放置目录。
	
	/usr/local本机自行安装的软件放置默认目录，当前也是用/opt目录。
	
	/usr/share一些帮助文件。
	
	/usr/srcliunx程序相管的程序代码放置目录。
	
	/usr/X11R6程序内的X WindowSystem所需的执行文件几乎都放在此。

14. /var 

	主要放置系统执行过程中经常变化的文件。

    /var/cache 程序文件行过程中的一些暂存盘。

	/var/lib 程序执行过程中，使用的数据文件存至目录。
	
	/var/log 登录文件放置的目录。
	
	/var/lock 某些设备具有一次性写入的特性。
	
	/var/run 默写程序或服务启动后，会将它们的PID放在这一目录下。目录中有套接字。
	
	/var/spool 是一列队列数据存放的地方。

文件颜色的含义：**蓝色为文件夹；绿色是可执行文件；浅蓝色是链接文件；红框文件是加了SUID位，任意限权；红色为压缩文件；褐色为设备文件**。



## 特殊符号表示不同的文件夹

```
.代表此层目录
..代表上层目录
-代表前一个工作目录
~代表[当前使用者身份]所在的home目录
```



# CMD-windows命令

## netstat-选项

```
-a 显示所有socket，��括正在监听的。
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

## 静态和动态库

* 静态库
  * 静态库的制作

	count.c
		
		int mul(int a, int b){
			return a*b;
		}
	
	gcc -c count.c		//先生成.o文件

	ar crv countlib.a count.o//将.o文件归档

  *	静态库的使用

	main.c

		#include <stdio.h>
		#include <stdlib.h>

		int main(char argc, int **argv){

				int a,b;
				sscanf(argv[1], "%d", &a);
				sscanf(argv[2], "%d", &b);
				printf("the result is:%d\n",count(a,b));
				return 0;
		}

	gcc main.c -L ./ countlib.a -o cal			//生成可执行文件

	./cal 3 16		//使用可执行文件进行计算


* 动态链接库
  * 动态链接库的制作

	gcc -fPIC -c countlib.c		
	//先生成与位置无关的目标文件

	gcc -shared countlib.o -o countlib.so
	//生成动态链接库

  * 动态链接库的使用

	gcc main.c -o cal2 -L ./ countlib.so

	//编译的时候没有什么错误生成

	//但是在运行的时候会有错误，说找不到相应的库

	./cal2 2 4

	//发生这个错误的原因是：这个程序在运行的时候找不到对应的动态链接的库
	//要想正确运行这个程序的方法是：
	//1. 执行命令：./cal2 2 4 -L ./ countlib.so
	//2. 将countlib.so文件拷贝到/usr/lib下；当程序运行的时候系统会自动到路径下找动态链接
	//3. 将当前countlib.so的路径加入到系统在运行程序时会去自动找的文件的路径。这些文件夹的路径存放在etc/ld.so.conf.d/*.conf文件中
	//以上三种方法能够让依赖动态库的cal2程序运行





<h1 align="center"> shell脚本例子<h1>

### 实例1
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

### 实例2
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

### 实例3

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


