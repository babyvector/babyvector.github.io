
#Phil Storrs PC Hardware book
##Hardware types
- RAM
- ROM
##Memory allocations
- Base Memory
- ExTended memory
- Expanded(EMS) memory
- XMS memory
- Virtul memory
- 


+------------------+  <- 0xFFFFFFFF (4GB)
|      32-bit      |
|  memory mapped   |
|     devices      |
|                  |
/\/\/\/\/\/\/\/\/\/\

/\/\/\/\/\/\/\/\/\/\
|                  |
|      Unused      |
|                  |
+------------------+  <- depends on amount of RAM
|                  |
|                  |
| Extended Memory  |
|                  |
|                  |
+------------------+  <- 0x00100000 (1MB)
|     BIOS ROM     |
+------------------+  <- 0x000F0000 (960KB)
|  16-bit devices, |
|  expansion ROMs  |
+------------------+  <- 0x000C0000 (768KB)
|   VGA Display    |
+------------------+  <- 0x000A0000 (640KB)
|                  |
|    Low Memory    |
|                  |
+------------------+  <- 0x00000000

## BIOS的工作

Testing RAM is only one of the start up routines in the BIOS ROMs many start up responsibilities. The POST runs reliability tests on your RAM, BIOS ROM, Processor and special I/O chips. It then goes on to run CONFIGURATION routines that configure the various support chips that work with the microprocessor, determines what optional equipment is attached, gives adapters the chance to initialize themselves, sets up the Interrupt Vector Table in low memory, gets the keyboard ready to send keystrokes to the CPU, and invokes the BOOTSTRAP LOADER, which in turn loads the Operating System. After this final step, you see the first evidence that the Operating System is up and running, the command-line prompt under DOS, or the GUI under Windows.

1. 检查每一字节的RAM并将状态显示到屏幕上

##基本内存

基本内存有1MB在原始的电脑中，这些区域分为两个部分
- 常规内存 从00000-9FFFF（0到640KB）
- 保留内存 从A0000-FFFFF（640KB到1MB）

常规内存：（用户使用内存或者基本内存）
常规内存部分非常拥挤。After configuring DOS and loading device drivers and resident programs, there may be about 400 to 600 K byte of free space for applications. This may not be enough memory to run some applications, and those applications that do run may not be as efficient as they would be with more memory available.
Conventional memory is especially at a premium when the computer is running on a network, where the drivers can consume 100 K byte or more of RAM memory.
保留内存：
640KB之上的384KB是为系统保存的，保留的内存是用于视频RAM，系统BIOS ROM和任何BIOS拓展ROMS和任何网卡RAM和SCSI接口RAM。一般情况下应用程序不能访问这个区域。


## 芯片内部一般有二级告诉缓存

## 硬盘都带有告诉RAM缓存例如16M的

##计算机中的中断号一共16个


