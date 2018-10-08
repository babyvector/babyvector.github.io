---
layout: post
title: 操作系统|jos|lab2|lab2-note
categories: [操作系统]
---

```

	#include <inc/mmu.h>
	#include <inc/memlayout.h>
	
	pte_t entry_pgtable[NPTENTRIES];
	
	// The entry.S page directory maps the first 4MB of physical memory
	// starting at virtual address KERNBASE (that is, it maps virtual
	// addresses [KERNBASE, KERNBASE+4MB) to physical addresses [0, 4MB)).
	// We choose 4MB because that's how much we can map with one page
	// table and it's enough to get us through early boot.  We also map
	// virtual addresses [0, 4MB) to physical addresses [0, 4MB); this
	// region is critical for a few instructions in entry.S and then we
	// never use it again.
	//
	// Page directories (and page tables), must start on a page boundary,
	// hence the "__aligned__" attribute.  Also, because of restrictions
	// related to linking and static initializers, we use "x + PTE_P"
	// here, rather than the more standard "x | PTE_P".  Everywhere else
	// you should use "|" to combine flags.
	__attribute__((__aligned__(PGSIZE)))
	pde_t entry_pgdir[NPDENTRIES] = {
	        // Map VA's [0, 4MB) to PA's [0, 4MB)
	        [0]
	                = ((uintptr_t)entry_pgtable - KERNBASE) + PTE_P,
	        // Map VA's [KERNBASE, KERNBASE+4MB) to PA's [0, 4MB)
	        [KERNBASE>>PDXSHIFT]
	                = ((uintptr_t)entry_pgtable - KERNBASE) + PTE_P + PTE_W
	};
	// Entry 0 of the page table maps to physical page 0, entry 1 to
	// physical page 1, etc.
	__attribute__((__aligned__(PGSIZE)))
	pte_t entry_pgtable[NPTENTRIES] = {
	        0x000000 | PTE_P | PTE_W,
	        0x001000 | PTE_P | PTE_W,
	        0x002000 | PTE_P | PTE_W,
	        0x003000 | PTE_P | PTE_W,
	...

```

在进行boot的时候我们已经在这个文件中定义了分页结构所需要的所有东西包括：

页表表目录 entry_pgdir(1024个2^10)
|---------------------|
|---------------------|
|---------------------|
|---------------------|
|---------------------|
|---------------------|----->页表 entry_pgtable（1024个2^10）
|---------------------|		 |-----------------------|
|---------------------|		 |-----------------------|----->4K大小的page
|---------------------|		 |-----------------------|
|---------------------|		 |-----------------------|
|...						 |-----------------------|
 ..							 |-----------------------|
 (1)						 |-----------------------|
							 |...
							  ..
							  (2)
所以一共1024*1024*4K = 4GB


* 以下是线性地址

| | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | |

|-------------------|-------------------|-----------------------|
   Dir entry index      Table  index             offset        

* 前两个段进行页表变换
	1. entry_pgdir[Dir entry index] 中的数据也是一个索引后几位是代表权限
	2. entry_pgtable[Table index]中的数据的前20位代表page所在位置后几位代表访问page权限
	
* 最后一个段offset直接由线性地址中得到




