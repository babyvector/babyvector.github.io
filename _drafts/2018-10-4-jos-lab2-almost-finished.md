---
layout: post
title: 操作系统|jos|lab2|pgdir_walk()
categories: [操作系统]
---

###我们应该明确那些事情该是由page_insert()做的那些该由pgdir_walk()来作

在check_page()函数中的

va = (void*)(PGSIZE*NPDENTRIES + PGSIZE);
ptep = pgdir_walk(kern_pgdir,va,1);
ptep1 = (pte_t*)KADDR(PTE_ADDR(kern_pgdir[PDX(va)]));
assert(ptep == ptep1+PTX(va));

这里发生错误错误的原因就是由于page_insert()和pgdir_walk()的权责分配不清造成的影响。所以如果想让上面的通过我们必须在pgdir_walk()中将指向pages的table的page插入到kern_pgdir[PDX(va)]中。**同时**之后将在page_insert()做的同样的动作删除。



### mem_init()开始

1. 我们首先使用了page_init();
然后我们建立一个page_free_list链表
包括

	* page_insert();
	* page_alloc();
	* pgdir_walk();
	* page_remove();
	* page_free();
	* page_free();
等一系列的操作。这些操作配合page_free_list将电脑中的所有的剩余的内存管理了起来。
2. boot_map_region(kern_pgdir,UPAGES,0x400000,PADDR(pages),PTE_U|PTE_P);
将所有的'pages'以只读的方式映射到线性地址UPAGES的地方。

3. boot_map_region(kern_pgdir,KSTACKTOP-KSTKSIZE,KSTKSIZE,PADDR(bootstack),PTE_W|PTE_A);将'bootstack'映射到系统为整个系统运行准备的堆栈KSTACKTOP处。
4. boot_map_region(kern_pgdir,KERNBASE,(size_t)PGSIZE*1024*1024-KERNBASE,0,PTE_W|PTE_A);将Map all of physical memory at KERNBASE. Ie.  the VA range [KERNBASE, 2^32) should map to the PA range [0, 2^32 - KERNBASE)


我想在以后的操作系统中我们系统中的所有的内存肯定都是通过1建立的结构进行内存的管理的2，3步将所需要的数据结构内容映射到相应的位置，4确保了系统在进行新的页表转换之后能够正常运行(?)




##error
0xffffffff是32系统中最大的数字所以我们在使用for循环的时候要十分注意这个细节。
