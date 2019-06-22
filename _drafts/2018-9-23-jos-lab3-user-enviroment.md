---
layout: post
title: 操作系统|jos|lab3-User Environments
categories: [操作系统]
---

	i386_init();->
	
	mem_init();->
	
	env_init();->
	
	trap_init();->
	
	ENV_CREATE(user_divzero,ENV_TYPE_USER);->
	
	env_run(&envs[0]);->
	
		第一个环境在运行的时候，陷入到之前定义的trap()中；
		trap();->
		trap_dispatch();//通过传入的Trapefame来识别是什么类型的中的段之后我们要编写相应的函数进行处理


x86结构中会自动的执行一些命令：

The processor can take exceptions and interrupts both from kernel and user mode. It is only when entering the kernel from user mode, however, that the x86 processor automatically switches stacks before pushing its old register state onto the stack and invoking the appropriate exception handler through the IDT.

	x86CPU会根据不同异步中断或者同步异常的状态，然后通过在trap文件中初始化的TSS来读取内核堆栈段和内核堆栈指针然后将需要保存的数据保存按照一定的规则保存到相应的位置。

* Chanllege

	Challenge! Modify the JOS kernel monitor so that you can 'continue' execution from the current location (e.g., after the int3, if the kernel monitor was invoked via the breakpoint exception), and so that you can single-step one instruction at a time. You will need to understand certain bits of the EFLAGS register in order to implement single-stepping.

	对于这个Chanllege可以在trap_dispatch()函数中对3号中断进行处理。然后使用ENV_CREATE（user_breakpoint）去进行实验，可以看到cpu运行到user_breakpoint函数后进入了异常，然后异常调用了monitor(null)；然后再k> quit；之后跳出异常然后再次运行再次调用monitor(NULL);

	如何实现单步调试：根据提示个人猜想是，可以设置FLAGS标志位，然后每一次单步，cpu就会进入异常（可能是这样吧，哈哈）





