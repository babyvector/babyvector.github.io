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

