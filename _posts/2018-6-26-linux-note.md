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
3. vim中的进行撤销的命令
	U

## linux中的ps

ps命令用于查看系统中正在运行的进程
参数格式：

- e 显示所有进程。
- f 全格式。
- h 不显示标题。
- l 长格式。
- w 宽输出。
- a 显示终端上的所有进程，包括其他用户的进程。
- r 只显示正在运行的进程。
- x 显示没有控制终端的进程。

最常用的几个命令

- ps -ef 查看全格式的全部进程
- ps -ax 查看全部进程
- ps -ef|grep <进程名> 查看并筛选 跟进程名有关的进程，该进程名可以是进程的全部或者部分

## linux中的netstat命令

netstat命令用来打印Linux中网络系统的状态信息，可让你得知整个Linux系统的网络情况。

语法

netstat(选项)

选项

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

实例

列出所有端口 (包括监听和未监听的)

- netstat -a     #列出所有端口
- netstat -at    #列出所有tcp端口
- netstat -au    #列出所有udp端口     
-                         
列出所有处于监听状态的 Sockets

- netstat -l        #只显示监听端口
- netstat -lt       #只列出所有监听 tcp 端口
- netstat -lu       #只列出所有监听 udp 端口
- netstat -lx       #只列出所有监听 UNIX 端口

dd
在netstat输出中显示 PID 和进程名称

- netstat -pt

[详细查看](http://man.linuxde.net/netstat)

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


## linux中的dd命令

参数注释：

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

二、dd应用实例

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

#dd if=/dev/mem of=/root/mem.bin bs=1024 (指定块大小为1k)  

9.拷贝光盘内容到指定文件夹，并保存为cd.iso文件

#dd if=/dev/cdrom(hdc) of=/root/cd.iso

10.增加swap分区文件大小

第一步：创建一个大小为256M的文件：

#dd if=/dev/zero of=/swapfile bs=1024 count=262144

第二步：把这个文件变成swap文件：

#mkswap /swapfile

第三步：启用这个swap文件：

#swapon /swapfile

第四步：编辑/etc/fstab文件，使在每次开机时自动加载swap文件：

/swapfile    swap    swap    default   0 0

11.销毁磁盘数据

#dd if=/dev/urandom of=/dev/hda1

注意：利用随机的数据填充硬盘，在某些必要的场合可以用来销毁数据。

12.测试硬盘的读写速度

#dd if=/dev/zero bs=1024 count=1000000 of=/root/1Gb.file

#dd if=/root/1Gb.file bs=64k | dd of=/dev/null

通过以上两个命令输出的命令执行时间，可以计算出硬盘的读、写速度。

13.确定硬盘的最佳块大小：

#dd if=/dev/zero bs=1024 count=1000000 of=/root/1Gb.file

#dd if=/dev/zero bs=2048 count=500000 of=/root/1Gb.file

#dd if=/dev/zero bs=4096 count=250000 of=/root/1Gb.file

#dd if=/dev/zero bs=8192 count=125000 of=/root/1Gb.file

通过比较以上命令输出中所显示的命令执行时间，即可确定系统最佳的块大小。

14.修复硬盘：

#dd if=/dev/sda of=/dev/sda 或dd if=/dev/hda of=/dev/hda

当硬盘较长时间(一年以上)放置不使用后，磁盘上会产生magnetic flux point，当磁头读到这些区域时会遇到困难，并可能导致I/O错误。当这种情况影响到硬盘的第一个扇区时，可能导致硬盘报废。上边的命令有可能使这些数 据起死回生。并且这个过程是安全、高效的。

15.利用netcat远程备份

#dd if=/dev/hda bs=16065b | netcat < targethost-IP > 1234

在源主机上执行此命令备份/dev/hda

#netcat -l -p 1234 | dd of=/dev/hdc bs=16065b

在目的主机上执行此命令来接收数据并写入/dev/hdc

#netcat -l -p 1234 | bzip2 > partition.img

#netcat -l -p 1234 | gzip > partition.img

以上两条指令是目的主机指令的变化分别采用bzip2、gzip对数据进行压缩，并将备份文件保存在当前目录。

16.将一个大视频文件的第i个字节的值改成0x41（大写字母A的ASCII值）

#echo A | dd of=bigfile seek=$i bs=1 count=1 conv=notrunc

17.建立linux虚拟盘，用文件模拟磁盘

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

三、/dev/null和/dev/zero的区别
/dev/null，外号叫无底洞，你可以向它输出任何数据，它通吃，并且不会撑着！

/dev/zero，是一个输入设备，你可你用它来初始化文件。该设备无穷尽地提供0，可以使用任何你需要的数目——设备提供的要多的多。他可以用于向设备或文件写入字符串0。

/dev/null------它是空设备，也称为位桶（bit bucket）。任何写入它的输出都会被抛弃。如果不想让消息以标准输出显示或写入文件，那么可以将消息重定向到位桶。

#if=/dev/zero of=./test.txt bs=1k count=1
#ls –l

total 4
-rw-r--r--    1 oracle   dba          1024 Jul 15 16:56 test.txt

#find / -name access_log  2>/dev/null

3.1使用/dev/null 
把/dev/null看作"黑洞"， 它等价于一个只写文件，所有写入它的内容都会永远丢失.，而尝试从它那儿读取内容则什么也读不到。然而， /dev/null对命令行和脚本都非常的有用

禁止标准输出

#cat $filename >/dev/null

文件内容丢失，而不会输出到标准输出.

禁止标准错误

#rm $badname 2>/dev/null

这样错误信息[标准错误]就被丢到太平洋去了

禁止标准输出和标准错误的输出

#cat $filename 2>/dev/null >/dev/null

如果"filename"不存在，将不会有任何错误信息提示；如果"filename"存在， 文件的内容不会打印到标准输出。

因此，上面的代码根本不会输出任何信息。当只想测试命令的退出码而不想有任何输出时非常有用。

#cat $filename &>/dev/null

这样其实也可以， 由 Baris Cicek 指出

自动清空日志文件的内容

Deleting contents of a file, but preserving the file itself, with all attendant permissions (from Example 2-1 and Example 2-3): 

#cat /dev/null > /var/log/messages
#  : > /var/log/messages   有同样的效果， 但不会产生新的进程.（因为:是内建的）
#cat /dev/null > /var/log/wtmp

特别适合处理这些由商业Web站点发送的讨厌的"cookies"

隐藏cookie而不再使用

#if [ -f ~/.netscape/cookies ]  # 如果存在则删除.
#then
#rm -f ~/.netscape/cookies
#fi
#ln -s /dev/null ~/.netscape/cookies

现在所有的cookies都会丢入黑洞而不会保存在磁盘上了.

3.2使用/dev/zero
像/dev/null一样， /dev/zero也是一个伪文件， 但它实际上产生连续不断的null的流（二进制的零流，而不是ASCII型的）。 写入它的输出会丢失不见， 而从/dev/zero读出一连串的null也比较困难， 虽然这也能通过od或一个十六进制编辑器来做到。 /dev/zero主要的用处是用来创建一个指定长度用于初始化的空文件，就像临时交换文件

用/dev/zero创建一个交换临时文件

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

关于 /dev/zero 的另一个应用是为特定的目的而用零去填充一个指定大小的文件， 如挂载一个文件系统到环回设备 （loopback device）或"安全地" 删除一个文件

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

