---
layout: post
title: jos 操作系统文件构成结构
categories: [操作系统]
tags: OS
---
jos的文件结构
boot
	boot.S
	main.c
kern
	kern/entry.S \
	kern/entrypgdir.c \
	kern/init.c \
	kern/console.c \
	kern/monitor.c \
	kern/pmap.c \
	kern/env.c \
	kern/kclock.c \
	kern/picirq.c \
	kern/printf.c \
	kern/trap.c \
	kern/trapentry.S \
	kern/sched.c \
	kern/syscall.c \
	kern/kdebug.c \
	lib/printfmt.c \
	lib/readline.c \
	lib/string.c


####boot.S
```
#include <inc/mmu.h>

# Start the CPU: switch to 32-bit protected mode, jump into C.
# The BIOS loads this code from the first sector of the hard disk into
# memory at physical address 0x7c00 and starts executing in real mode
# with %cs=0 %ip=7c00.

.set PROT_MODE_CSEG, 0x8         # kernel code segment selector
.set PROT_MODE_DSEG, 0x10        # kernel data segment selector
.set CR0_PE_ON,      0x1         # protected mode enable flag

.globl start
start:
  .code16                     # Assemble for 16-bit mode
  cli                         # Disable interrupts
  cld                         # String operations increment

  # Set up the important data segment registers (DS, ES, SS).
  xorw    %ax,%ax             # Segment number zero
  movw    %ax,%ds             # -> Data Segment
  movw    %ax,%es             # -> Extra Segment
  movw    %ax,%ss             # -> Stack Segment

  # Enable A20:
  #   For backwards compatibility with the earliest PCs, physical
  #   address line 20 is tied low, so that addresses higher than
  #   1MB wrap around to zero by default.  This code undoes this.
seta20.1:
  inb     $0x64,%al               # Wait for not busy
  testb   $0x2,%al
  jnz     seta20.1

  movb    $0xd1,%al               # 0xd1 -> port 0x64
  outb    %al,$0x64

seta20.2:
  inb     $0x64,%al               # Wait for not busy
  testb   $0x2,%al
  jnz     seta20.2

  movb    $0xdf,%al               # 0xdf -> port 0x60
  outb    %al,$0x60

  # Switch from real to protected mode, using a bootstrap GDT
  # and segment translation that makes virtual addresses 
  # identical to their physical addresses, so that the 
  # effective memory map does not change during the switch.
  lgdt    gdtdesc
  movl    %cr0, %eax
  orl     $CR0_PE_ON, %eax
  movl    %eax, %cr0
  
  # Jump to next instruction, but in 32-bit code segment.
  # Switches processor into 32-bit mode.
  ljmp    $PROT_MODE_CSEG, $protcseg

  .code32                     # Assemble for 32-bit mode
protcseg:
  # Set up the protected-mode data segment registers
  movw    $PROT_MODE_DSEG, %ax    # Our data segment selector
  movw    %ax, %ds                # -> DS: Data Segment
  movw    %ax, %es                # -> ES: Extra Segment
  movw    %ax, %fs                # -> FS
  movw    %ax, %gs                # -> GS
  movw    %ax, %ss                # -> SS: Stack Segment
  
  # Set up the stack pointer and call into C.
  movl    $start, %esp
  call bootmain

  # If bootmain returns (it shouldn't), loop.
spin:
  jmp spin

# Bootstrap GDT
.p2align 2                                # force 4 byte alignment
gdt:
  SEG_NULL				# null seg
  SEG(STA_X|STA_R, 0x0, 0xffffffff)	# code seg
  SEG(STA_W, 0x0, 0xffffffff)	        # data seg

gdtdesc:
  .word   0x17                            # sizeof(gdt) - 1
  .long   gdt                             # address gdt


```
####main.c
```
#include <inc/x86.h>
#include <inc/elf.h>

/**********************************************************************
 * This a dirt simple boot loader, whose sole job is to boot
 * an ELF kernel image from the first IDE hard disk.
 *
 * DISK LAYOUT
 *  * This program(boot.S and main.c) is the bootloader.  It should
 *    be stored in the first sector of the disk.
 *
 *  * The 2nd sector onward holds the kernel image.
 *
 *  * The kernel image must be in ELF format.
 *
 * BOOT UP STEPS
 *  * when the CPU boots it loads the BIOS into memory and executes it
 *
 *  * the BIOS intializes devices, sets of the interrupt routines, and
 *    reads the first sector of the boot device(e.g., hard-drive)
 *    into memory and jumps to it.
 *
 *  * Assuming this boot loader is stored in the first sector of the
 *    hard-drive, this code takes over...
 *
 *  * control starts in boot.S -- which sets up protected mode,
 *    and a stack so C code then run, then calls bootmain()
 *
 *  * bootmain() in this file takes over, reads in the kernel and jumps to it.
 **********************************************************************/

#define SECTSIZE	512
#define ELFHDR		((struct Elf *) 0x10000) // scratch space

void readsect(void*, uint32_t);
void readseg(uint32_t, uint32_t, uint32_t);

void
bootmain(void)
{
	struct Proghdr *ph, *eph;

	// read 1st page off disk
	readseg((uint32_t) ELFHDR, SECTSIZE*8, 0);

	// is this a valid ELF?
	if (ELFHDR->e_magic != ELF_MAGIC)
		goto bad;

	// load each program segment (ignores ph flags)
	ph = (struct Proghdr *) ((uint8_t *) ELFHDR + ELFHDR->e_phoff);
	eph = ph + ELFHDR->e_phnum;
	for (; ph < eph; ph++)
		// p_pa is the load address of this segment (as well
		// as the physical address)
		readseg(ph->p_pa, ph->p_memsz, ph->p_offset);

	// call the entry point from the ELF header
	// note: does not return!
	((void (*)(void)) (ELFHDR->e_entry))();

bad:
	outw(0x8A00, 0x8A00);
	outw(0x8A00, 0x8E00);
	while (1)
		/* do nothing */;
}

// Read 'count' bytes at 'offset' from kernel into physical address 'pa'.
// Might copy more than asked
void
readseg(uint32_t pa, uint32_t count, uint32_t offset)
{
	uint32_t end_pa;

	end_pa = pa + count;

	// round down to sector boundary
	pa &= ~(SECTSIZE - 1);

	// translate from bytes to sectors, and kernel starts at sector 1
	offset = (offset / SECTSIZE) + 1;

	// If this is too slow, we could read lots of sectors at a time.
	// We'd write more to memory than asked, but it doesn't matter --
	// we load in increasing order.
	while (pa < end_pa) {
		// Since we haven't enabled paging yet and we're using
		// an identity segment mapping (see boot.S), we can
		// use physical addresses directly.  This won't be the
		// case once JOS enables the MMU.
		readsect((uint8_t*) pa, offset);
		pa += SECTSIZE;
		offset++;
	}
}

void
waitdisk(void)
{
	// wait for disk reaady
	while ((inb(0x1F7) & 0xC0) != 0x40)
		/* do nothing */;
}

void
readsect(void *dst, uint32_t offset)
{
	// wait for disk to be ready
	waitdisk();

	outb(0x1F2, 1);		// count = 1
	outb(0x1F3, offset);
	outb(0x1F4, offset >> 8);
	outb(0x1F5, offset >> 16);
	outb(0x1F6, (offset >> 24) | 0xE0);
	outb(0x1F7, 0x20);	// cmd 0x20 - read sectors

	// wait for disk to be ready
	waitdisk();

	// read a sector
	insl(0x1F0, dst, SECTSIZE/4);
}


```

####entry.S
```
/* See COPYRIGHT for copyright information. */

#include <inc/mmu.h>
#include <inc/memlayout.h>

# Shift Right Logical 
#define SRL(val, shamt)		(((val) >> (shamt)) & ~(-1 << (32 - (shamt))))


###################################################################
# The kernel (this code) is linked at address ~(KERNBASE + 1 Meg), 
# but the bootloader loads it at address ~1 Meg.
#	
# RELOC(x) maps a symbol x from its link address to its actual
# location in physical memory (its load address).	 
###################################################################

#define	RELOC(x) ((x) - KERNBASE)

#define MULTIBOOT_HEADER_MAGIC (0x1BADB002)
#define MULTIBOOT_HEADER_FLAGS (0)
#define CHECKSUM (-(MULTIBOOT_HEADER_MAGIC + MULTIBOOT_HEADER_FLAGS))

###################################################################
# entry point
###################################################################

.text

# The Multiboot header
.align 4
.long MULTIBOOT_HEADER_MAGIC
.long MULTIBOOT_HEADER_FLAGS
.long CHECKSUM

# '_start' specifies the ELF entry point.  Since we haven't set up
# virtual memory when the bootloader enters this code, we need the
# bootloader to jump to the *physical* address of the entry point.
.globl		_start
_start = RELOC(entry)

.globl entry
entry:
	movw	$0x1234,0x472			# warm boot

	# We haven't set up virtual memory yet, so we're running from
	# the physical address the boot loader loaded the kernel at: 1MB
	# (plus a few bytes).  However, the C code is linked to run at
	# KERNBASE+1MB.  Hence, we set up a trivial page directory that
	# translates virtual addresses [KERNBASE, KERNBASE+4MB) to
	# physical addresses [0, 4MB).  This 4MB region will be
	# sufficient until we set up our real page table in mem_init
	# in lab 2.

	# Load the physical address of entry_pgdir into cr3.  entry_pgdir
	# is defined in entrypgdir.c.
	movl	$(RELOC(entry_pgdir)), %eax
	movl	%eax, %cr3
	# Turn on paging.
	movl	%cr0, %eax
	orl	$(CR0_PE|CR0_PG|CR0_WP), %eax
	movl	%eax, %cr0

	# Now paging is enabled, but we're still running at a low EIP
	# (why is this okay?).  Jump up above KERNBASE before entering
	# C code.
	mov	$relocated, %eax
	jmp	*%eax
relocated:

	# Clear the frame pointer register (EBP)
	# so that once we get into debugging C code,
	# stack backtraces will be terminated properly.
	movl	$0x0,%ebp			# nuke frame pointer

	# Set the stack pointer
	movl	$(bootstacktop),%esp

	# now to C code
	call	i386_init

	# Should never get here, but in case we do, just spin.
spin:	jmp	spin


.data
###################################################################
# boot stack
###################################################################
	.p2align	PGSHIFT		# force page alignment
	.globl		bootstack
bootstack:
	.space		KSTKSIZE
	.globl		bootstacktop   
bootstacktop:


```
####entrypgdir.c 
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
	0x004000 | PTE_P | PTE_W,
	0x005000 | PTE_P | PTE_W,
	0x006000 | PTE_P | PTE_W,
	0x007000 | PTE_P | PTE_W,
	0x008000 | PTE_P | PTE_W,
	0x009000 | PTE_P | PTE_W,
	0x00a000 | PTE_P | PTE_W,
	0x00b000 | PTE_P | PTE_W,
	0x00c000 | PTE_P | PTE_W,
	0x00d000 | PTE_P | PTE_W,
	0x00e000 | PTE_P | PTE_W,
	0x00f000 | PTE_P | PTE_W,
	0x010000 | PTE_P | PTE_W,
	0x011000 | PTE_P | PTE_W,
	0x012000 | PTE_P | PTE_W,
	0x013000 | PTE_P | PTE_W,
	0x014000 | PTE_P | PTE_W,
	0x015000 | PTE_P | PTE_W,
	0x016000 | PTE_P | PTE_W,
	0x017000 | PTE_P | PTE_W,
	0x018000 | PTE_P | PTE_W,
	0x019000 | PTE_P | PTE_W,
	0x01a000 | PTE_P | PTE_W,
	0x01b000 | PTE_P | PTE_W,
	0x01c000 | PTE_P | PTE_W,
	0x01d000 | PTE_P | PTE_W,
	0x01e000 | PTE_P | PTE_W,
	0x01f000 | PTE_P | PTE_W,
	0x020000 | PTE_P | PTE_W,
	0x021000 | PTE_P | PTE_W,
	0x022000 | PTE_P | PTE_W,
	0x023000 | PTE_P | PTE_W,
	0x024000 | PTE_P | PTE_W,
	0x025000 | PTE_P | PTE_W,
	0x026000 | PTE_P | PTE_W,
	0x027000 | PTE_P | PTE_W,
	0x028000 | PTE_P | PTE_W,
	0x029000 | PTE_P | PTE_W,
	0x02a000 | PTE_P | PTE_W,
	0x02b000 | PTE_P | PTE_W,
	0x02c000 | PTE_P | PTE_W,
	0x02d000 | PTE_P | PTE_W,
	0x02e000 | PTE_P | PTE_W,
	0x02f000 | PTE_P | PTE_W,
	0x030000 | PTE_P | PTE_W,
	0x031000 | PTE_P | PTE_W,
	0x032000 | PTE_P | PTE_W,
	0x033000 | PTE_P | PTE_W,
	0x034000 | PTE_P | PTE_W,
	0x035000 | PTE_P | PTE_W,
	0x036000 | PTE_P | PTE_W,
	0x037000 | PTE_P | PTE_W,
	0x038000 | PTE_P | PTE_W,
	0x039000 | PTE_P | PTE_W,
	0x03a000 | PTE_P | PTE_W,
	0x03b000 | PTE_P | PTE_W,
	0x03c000 | PTE_P | PTE_W,
	0x03d000 | PTE_P | PTE_W,
	0x03e000 | PTE_P | PTE_W,
	0x03f000 | PTE_P | PTE_W,
	0x040000 | PTE_P | PTE_W,
	0x041000 | PTE_P | PTE_W,
	0x042000 | PTE_P | PTE_W,
	0x043000 | PTE_P | PTE_W,
	0x044000 | PTE_P | PTE_W,
	0x045000 | PTE_P | PTE_W,
	0x046000 | PTE_P | PTE_W,
	0x047000 | PTE_P | PTE_W,
	0x048000 | PTE_P | PTE_W,
	0x049000 | PTE_P | PTE_W,
	0x04a000 | PTE_P | PTE_W,
	0x04b000 | PTE_P | PTE_W,
	0x04c000 | PTE_P | PTE_W,
	0x04d000 | PTE_P | PTE_W,
	0x04e000 | PTE_P | PTE_W,
	0x04f000 | PTE_P | PTE_W,
	0x050000 | PTE_P | PTE_W,
	0x051000 | PTE_P | PTE_W,
	0x052000 | PTE_P | PTE_W,
	0x053000 | PTE_P | PTE_W,
	0x054000 | PTE_P | PTE_W,
	0x055000 | PTE_P | PTE_W,
	0x056000 | PTE_P | PTE_W,
	0x057000 | PTE_P | PTE_W,
	0x058000 | PTE_P | PTE_W,
	0x059000 | PTE_P | PTE_W,
	0x05a000 | PTE_P | PTE_W,
	0x05b000 | PTE_P | PTE_W,
	0x05c000 | PTE_P | PTE_W,
	0x05d000 | PTE_P | PTE_W,
	0x05e000 | PTE_P | PTE_W,
	0x05f000 | PTE_P | PTE_W,
	0x060000 | PTE_P | PTE_W,
	0x061000 | PTE_P | PTE_W,
	0x062000 | PTE_P | PTE_W,
	0x063000 | PTE_P | PTE_W,
	0x064000 | PTE_P | PTE_W,
	0x065000 | PTE_P | PTE_W,
	0x066000 | PTE_P | PTE_W,
	0x067000 | PTE_P | PTE_W,
	0x068000 | PTE_P | PTE_W,
	0x069000 | PTE_P | PTE_W,
	0x06a000 | PTE_P | PTE_W,
	0x06b000 | PTE_P | PTE_W,
	0x06c000 | PTE_P | PTE_W,
	0x06d000 | PTE_P | PTE_W,
	0x06e000 | PTE_P | PTE_W,
	0x06f000 | PTE_P | PTE_W,
	0x070000 | PTE_P | PTE_W,
	0x071000 | PTE_P | PTE_W,
	0x072000 | PTE_P | PTE_W,
	0x073000 | PTE_P | PTE_W,
	0x074000 | PTE_P | PTE_W,
	0x075000 | PTE_P | PTE_W,
	0x076000 | PTE_P | PTE_W,
	0x077000 | PTE_P | PTE_W,
	0x078000 | PTE_P | PTE_W,
	0x079000 | PTE_P | PTE_W,
	0x07a000 | PTE_P | PTE_W,
	0x07b000 | PTE_P | PTE_W,
	0x07c000 | PTE_P | PTE_W,
	0x07d000 | PTE_P | PTE_W,
	0x07e000 | PTE_P | PTE_W,
	0x07f000 | PTE_P | PTE_W,
	0x080000 | PTE_P | PTE_W,
	0x081000 | PTE_P | PTE_W,
	0x082000 | PTE_P | PTE_W,
	0x083000 | PTE_P | PTE_W,
	0x084000 | PTE_P | PTE_W,
	0x085000 | PTE_P | PTE_W,
	0x086000 | PTE_P | PTE_W,
	0x087000 | PTE_P | PTE_W,
	0x088000 | PTE_P | PTE_W,
	0x089000 | PTE_P | PTE_W,
	0x08a000 | PTE_P | PTE_W,
	0x08b000 | PTE_P | PTE_W,
	0x08c000 | PTE_P | PTE_W,
	0x08d000 | PTE_P | PTE_W,
	0x08e000 | PTE_P | PTE_W,
	0x08f000 | PTE_P | PTE_W,
	0x090000 | PTE_P | PTE_W,
	0x091000 | PTE_P | PTE_W,
	0x092000 | PTE_P | PTE_W,
	0x093000 | PTE_P | PTE_W,
	0x094000 | PTE_P | PTE_W,
	0x095000 | PTE_P | PTE_W,
	0x096000 | PTE_P | PTE_W,
	0x097000 | PTE_P | PTE_W,
	0x098000 | PTE_P | PTE_W,
	0x099000 | PTE_P | PTE_W,
	0x09a000 | PTE_P | PTE_W,
	0x09b000 | PTE_P | PTE_W,
	0x09c000 | PTE_P | PTE_W,
	0x09d000 | PTE_P | PTE_W,
	0x09e000 | PTE_P | PTE_W,
	0x09f000 | PTE_P | PTE_W,
	0x0a0000 | PTE_P | PTE_W,
	0x0a1000 | PTE_P | PTE_W,
	0x0a2000 | PTE_P | PTE_W,
	0x0a3000 | PTE_P | PTE_W,
	0x0a4000 | PTE_P | PTE_W,
	0x0a5000 | PTE_P | PTE_W,
	0x0a6000 | PTE_P | PTE_W,
	0x0a7000 | PTE_P | PTE_W,
	0x0a8000 | PTE_P | PTE_W,
	0x0a9000 | PTE_P | PTE_W,
	0x0aa000 | PTE_P | PTE_W,
	0x0ab000 | PTE_P | PTE_W,
	0x0ac000 | PTE_P | PTE_W,
	0x0ad000 | PTE_P | PTE_W,
	0x0ae000 | PTE_P | PTE_W,
	0x0af000 | PTE_P | PTE_W,
	0x0b0000 | PTE_P | PTE_W,
	0x0b1000 | PTE_P | PTE_W,
	0x0b2000 | PTE_P | PTE_W,
	0x0b3000 | PTE_P | PTE_W,
	0x0b4000 | PTE_P | PTE_W,
	0x0b5000 | PTE_P | PTE_W,
	0x0b6000 | PTE_P | PTE_W,
	0x0b7000 | PTE_P | PTE_W,
	0x0b8000 | PTE_P | PTE_W,
	0x0b9000 | PTE_P | PTE_W,
	0x0ba000 | PTE_P | PTE_W,
	0x0bb000 | PTE_P | PTE_W,
	0x0bc000 | PTE_P | PTE_W,
	0x0bd000 | PTE_P | PTE_W,
	0x0be000 | PTE_P | PTE_W,
	0x0bf000 | PTE_P | PTE_W,
	0x0c0000 | PTE_P | PTE_W,
	0x0c1000 | PTE_P | PTE_W,
	0x0c2000 | PTE_P | PTE_W,
	0x0c3000 | PTE_P | PTE_W,
	0x0c4000 | PTE_P | PTE_W,
	0x0c5000 | PTE_P | PTE_W,
	0x0c6000 | PTE_P | PTE_W,
	0x0c7000 | PTE_P | PTE_W,
	0x0c8000 | PTE_P | PTE_W,
	0x0c9000 | PTE_P | PTE_W,
	0x0ca000 | PTE_P | PTE_W,
	0x0cb000 | PTE_P | PTE_W,
	0x0cc000 | PTE_P | PTE_W,
	0x0cd000 | PTE_P | PTE_W,
	0x0ce000 | PTE_P | PTE_W,
	0x0cf000 | PTE_P | PTE_W,
	0x0d0000 | PTE_P | PTE_W,
	0x0d1000 | PTE_P | PTE_W,
	0x0d2000 | PTE_P | PTE_W,
	0x0d3000 | PTE_P | PTE_W,
	0x0d4000 | PTE_P | PTE_W,
	0x0d5000 | PTE_P | PTE_W,
	0x0d6000 | PTE_P | PTE_W,
	0x0d7000 | PTE_P | PTE_W,
	0x0d8000 | PTE_P | PTE_W,
	0x0d9000 | PTE_P | PTE_W,
	0x0da000 | PTE_P | PTE_W,
	0x0db000 | PTE_P | PTE_W,
	0x0dc000 | PTE_P | PTE_W,
	0x0dd000 | PTE_P | PTE_W,
	0x0de000 | PTE_P | PTE_W,
	0x0df000 | PTE_P | PTE_W,
	0x0e0000 | PTE_P | PTE_W,
	0x0e1000 | PTE_P | PTE_W,
	0x0e2000 | PTE_P | PTE_W,
	0x0e3000 | PTE_P | PTE_W,
	0x0e4000 | PTE_P | PTE_W,
	0x0e5000 | PTE_P | PTE_W,
	0x0e6000 | PTE_P | PTE_W,
	0x0e7000 | PTE_P | PTE_W,
	0x0e8000 | PTE_P | PTE_W,
	0x0e9000 | PTE_P | PTE_W,
	0x0ea000 | PTE_P | PTE_W,
	0x0eb000 | PTE_P | PTE_W,
	0x0ec000 | PTE_P | PTE_W,
	0x0ed000 | PTE_P | PTE_W,
	0x0ee000 | PTE_P | PTE_W,
	0x0ef000 | PTE_P | PTE_W,
	0x0f0000 | PTE_P | PTE_W,
	0x0f1000 | PTE_P | PTE_W,
	0x0f2000 | PTE_P | PTE_W,
	0x0f3000 | PTE_P | PTE_W,
	0x0f4000 | PTE_P | PTE_W,
	0x0f5000 | PTE_P | PTE_W,
	0x0f6000 | PTE_P | PTE_W,
	0x0f7000 | PTE_P | PTE_W,
	0x0f8000 | PTE_P | PTE_W,
	0x0f9000 | PTE_P | PTE_W,
	0x0fa000 | PTE_P | PTE_W,
	0x0fb000 | PTE_P | PTE_W,
	0x0fc000 | PTE_P | PTE_W,
	0x0fd000 | PTE_P | PTE_W,
	0x0fe000 | PTE_P | PTE_W,
	0x0ff000 | PTE_P | PTE_W,
	0x100000 | PTE_P | PTE_W,
	0x101000 | PTE_P | PTE_W,
	0x102000 | PTE_P | PTE_W,
	0x103000 | PTE_P | PTE_W,
	0x104000 | PTE_P | PTE_W,
	0x105000 | PTE_P | PTE_W,
	0x106000 | PTE_P | PTE_W,
	0x107000 | PTE_P | PTE_W,
	0x108000 | PTE_P | PTE_W,
	0x109000 | PTE_P | PTE_W,
	0x10a000 | PTE_P | PTE_W,
	0x10b000 | PTE_P | PTE_W,
	0x10c000 | PTE_P | PTE_W,
	0x10d000 | PTE_P | PTE_W,
	0x10e000 | PTE_P | PTE_W,
	0x10f000 | PTE_P | PTE_W,
	0x110000 | PTE_P | PTE_W,
	0x111000 | PTE_P | PTE_W,
	0x112000 | PTE_P | PTE_W,
	0x113000 | PTE_P | PTE_W,
	0x114000 | PTE_P | PTE_W,
	0x115000 | PTE_P | PTE_W,
	0x116000 | PTE_P | PTE_W,
	0x117000 | PTE_P | PTE_W,
	0x118000 | PTE_P | PTE_W,
	0x119000 | PTE_P | PTE_W,
	0x11a000 | PTE_P | PTE_W,
	0x11b000 | PTE_P | PTE_W,
	0x11c000 | PTE_P | PTE_W,
	0x11d000 | PTE_P | PTE_W,
	0x11e000 | PTE_P | PTE_W,
	0x11f000 | PTE_P | PTE_W,
	0x120000 | PTE_P | PTE_W,
	0x121000 | PTE_P | PTE_W,
	0x122000 | PTE_P | PTE_W,
	0x123000 | PTE_P | PTE_W,
	0x124000 | PTE_P | PTE_W,
	0x125000 | PTE_P | PTE_W,
	0x126000 | PTE_P | PTE_W,
	0x127000 | PTE_P | PTE_W,
	0x128000 | PTE_P | PTE_W,
	0x129000 | PTE_P | PTE_W,
	0x12a000 | PTE_P | PTE_W,
	0x12b000 | PTE_P | PTE_W,
	0x12c000 | PTE_P | PTE_W,
	0x12d000 | PTE_P | PTE_W,
	0x12e000 | PTE_P | PTE_W,
	0x12f000 | PTE_P | PTE_W,
	0x130000 | PTE_P | PTE_W,
	0x131000 | PTE_P | PTE_W,
	0x132000 | PTE_P | PTE_W,
	0x133000 | PTE_P | PTE_W,
	0x134000 | PTE_P | PTE_W,
	0x135000 | PTE_P | PTE_W,
	0x136000 | PTE_P | PTE_W,
	0x137000 | PTE_P | PTE_W,
	0x138000 | PTE_P | PTE_W,
	0x139000 | PTE_P | PTE_W,
	0x13a000 | PTE_P | PTE_W,
	0x13b000 | PTE_P | PTE_W,
	0x13c000 | PTE_P | PTE_W,
	0x13d000 | PTE_P | PTE_W,
	0x13e000 | PTE_P | PTE_W,
	0x13f000 | PTE_P | PTE_W,
	0x140000 | PTE_P | PTE_W,
	0x141000 | PTE_P | PTE_W,
	0x142000 | PTE_P | PTE_W,
	0x143000 | PTE_P | PTE_W,
	0x144000 | PTE_P | PTE_W,
	0x145000 | PTE_P | PTE_W,
	0x146000 | PTE_P | PTE_W,
	0x147000 | PTE_P | PTE_W,
	0x148000 | PTE_P | PTE_W,
	0x149000 | PTE_P | PTE_W,
	0x14a000 | PTE_P | PTE_W,
	0x14b000 | PTE_P | PTE_W,
	0x14c000 | PTE_P | PTE_W,
	0x14d000 | PTE_P | PTE_W,
	0x14e000 | PTE_P | PTE_W,
	0x14f000 | PTE_P | PTE_W,
	0x150000 | PTE_P | PTE_W,
	0x151000 | PTE_P | PTE_W,
	0x152000 | PTE_P | PTE_W,
	0x153000 | PTE_P | PTE_W,
	0x154000 | PTE_P | PTE_W,
	0x155000 | PTE_P | PTE_W,
	0x156000 | PTE_P | PTE_W,
	0x157000 | PTE_P | PTE_W,
	0x158000 | PTE_P | PTE_W,
	0x159000 | PTE_P | PTE_W,
	0x15a000 | PTE_P | PTE_W,
	0x15b000 | PTE_P | PTE_W,
	0x15c000 | PTE_P | PTE_W,
	0x15d000 | PTE_P | PTE_W,
	0x15e000 | PTE_P | PTE_W,
	0x15f000 | PTE_P | PTE_W,
	0x160000 | PTE_P | PTE_W,
	0x161000 | PTE_P | PTE_W,
	0x162000 | PTE_P | PTE_W,
	0x163000 | PTE_P | PTE_W,
	0x164000 | PTE_P | PTE_W,
	0x165000 | PTE_P | PTE_W,
	0x166000 | PTE_P | PTE_W,
	0x167000 | PTE_P | PTE_W,
	0x168000 | PTE_P | PTE_W,
	0x169000 | PTE_P | PTE_W,
	0x16a000 | PTE_P | PTE_W,
	0x16b000 | PTE_P | PTE_W,
	0x16c000 | PTE_P | PTE_W,
	0x16d000 | PTE_P | PTE_W,
	0x16e000 | PTE_P | PTE_W,
	0x16f000 | PTE_P | PTE_W,
	0x170000 | PTE_P | PTE_W,
	0x171000 | PTE_P | PTE_W,
	0x172000 | PTE_P | PTE_W,
	0x173000 | PTE_P | PTE_W,
	0x174000 | PTE_P | PTE_W,
	0x175000 | PTE_P | PTE_W,
	0x176000 | PTE_P | PTE_W,
	0x177000 | PTE_P | PTE_W,
	0x178000 | PTE_P | PTE_W,
	0x179000 | PTE_P | PTE_W,
	0x17a000 | PTE_P | PTE_W,
	0x17b000 | PTE_P | PTE_W,
	0x17c000 | PTE_P | PTE_W,
	0x17d000 | PTE_P | PTE_W,
	0x17e000 | PTE_P | PTE_W,
	0x17f000 | PTE_P | PTE_W,
	0x180000 | PTE_P | PTE_W,
	0x181000 | PTE_P | PTE_W,
	0x182000 | PTE_P | PTE_W,
	0x183000 | PTE_P | PTE_W,
	0x184000 | PTE_P | PTE_W,
	0x185000 | PTE_P | PTE_W,
	0x186000 | PTE_P | PTE_W,
	0x187000 | PTE_P | PTE_W,
	0x188000 | PTE_P | PTE_W,
	0x189000 | PTE_P | PTE_W,
	0x18a000 | PTE_P | PTE_W,
	0x18b000 | PTE_P | PTE_W,
	0x18c000 | PTE_P | PTE_W,
	0x18d000 | PTE_P | PTE_W,
	0x18e000 | PTE_P | PTE_W,
	0x18f000 | PTE_P | PTE_W,
	0x190000 | PTE_P | PTE_W,
	0x191000 | PTE_P | PTE_W,
	0x192000 | PTE_P | PTE_W,
	0x193000 | PTE_P | PTE_W,
	0x194000 | PTE_P | PTE_W,
	0x195000 | PTE_P | PTE_W,
	0x196000 | PTE_P | PTE_W,
	0x197000 | PTE_P | PTE_W,
	0x198000 | PTE_P | PTE_W,
	0x199000 | PTE_P | PTE_W,
	0x19a000 | PTE_P | PTE_W,
	0x19b000 | PTE_P | PTE_W,
	0x19c000 | PTE_P | PTE_W,
	0x19d000 | PTE_P | PTE_W,
	0x19e000 | PTE_P | PTE_W,
	0x19f000 | PTE_P | PTE_W,
	0x1a0000 | PTE_P | PTE_W,
	0x1a1000 | PTE_P | PTE_W,
	0x1a2000 | PTE_P | PTE_W,
	0x1a3000 | PTE_P | PTE_W,
	0x1a4000 | PTE_P | PTE_W,
	0x1a5000 | PTE_P | PTE_W,
	0x1a6000 | PTE_P | PTE_W,
	0x1a7000 | PTE_P | PTE_W,
	0x1a8000 | PTE_P | PTE_W,
	0x1a9000 | PTE_P | PTE_W,
	0x1aa000 | PTE_P | PTE_W,
	0x1ab000 | PTE_P | PTE_W,
	0x1ac000 | PTE_P | PTE_W,
	0x1ad000 | PTE_P | PTE_W,
	0x1ae000 | PTE_P | PTE_W,
	0x1af000 | PTE_P | PTE_W,
	0x1b0000 | PTE_P | PTE_W,
	0x1b1000 | PTE_P | PTE_W,
	0x1b2000 | PTE_P | PTE_W,
	0x1b3000 | PTE_P | PTE_W,
	0x1b4000 | PTE_P | PTE_W,
	0x1b5000 | PTE_P | PTE_W,
	0x1b6000 | PTE_P | PTE_W,
	0x1b7000 | PTE_P | PTE_W,
	0x1b8000 | PTE_P | PTE_W,
	0x1b9000 | PTE_P | PTE_W,
	0x1ba000 | PTE_P | PTE_W,
	0x1bb000 | PTE_P | PTE_W,
	0x1bc000 | PTE_P | PTE_W,
	0x1bd000 | PTE_P | PTE_W,
	0x1be000 | PTE_P | PTE_W,
	0x1bf000 | PTE_P | PTE_W,
	0x1c0000 | PTE_P | PTE_W,
	0x1c1000 | PTE_P | PTE_W,
	0x1c2000 | PTE_P | PTE_W,
	0x1c3000 | PTE_P | PTE_W,
	0x1c4000 | PTE_P | PTE_W,
	0x1c5000 | PTE_P | PTE_W,
	0x1c6000 | PTE_P | PTE_W,
	0x1c7000 | PTE_P | PTE_W,
	0x1c8000 | PTE_P | PTE_W,
	0x1c9000 | PTE_P | PTE_W,
	0x1ca000 | PTE_P | PTE_W,
	0x1cb000 | PTE_P | PTE_W,
	0x1cc000 | PTE_P | PTE_W,
	0x1cd000 | PTE_P | PTE_W,
	0x1ce000 | PTE_P | PTE_W,
	0x1cf000 | PTE_P | PTE_W,
	0x1d0000 | PTE_P | PTE_W,
	0x1d1000 | PTE_P | PTE_W,
	0x1d2000 | PTE_P | PTE_W,
	0x1d3000 | PTE_P | PTE_W,
	0x1d4000 | PTE_P | PTE_W,
	0x1d5000 | PTE_P | PTE_W,
	0x1d6000 | PTE_P | PTE_W,
	0x1d7000 | PTE_P | PTE_W,
	0x1d8000 | PTE_P | PTE_W,
	0x1d9000 | PTE_P | PTE_W,
	0x1da000 | PTE_P | PTE_W,
	0x1db000 | PTE_P | PTE_W,
	0x1dc000 | PTE_P | PTE_W,
	0x1dd000 | PTE_P | PTE_W,
	0x1de000 | PTE_P | PTE_W,
	0x1df000 | PTE_P | PTE_W,
	0x1e0000 | PTE_P | PTE_W,
	0x1e1000 | PTE_P | PTE_W,
	0x1e2000 | PTE_P | PTE_W,
	0x1e3000 | PTE_P | PTE_W,
	0x1e4000 | PTE_P | PTE_W,
	0x1e5000 | PTE_P | PTE_W,
	0x1e6000 | PTE_P | PTE_W,
	0x1e7000 | PTE_P | PTE_W,
	0x1e8000 | PTE_P | PTE_W,
	0x1e9000 | PTE_P | PTE_W,
	0x1ea000 | PTE_P | PTE_W,
	0x1eb000 | PTE_P | PTE_W,
	0x1ec000 | PTE_P | PTE_W,
	0x1ed000 | PTE_P | PTE_W,
	0x1ee000 | PTE_P | PTE_W,
	0x1ef000 | PTE_P | PTE_W,
	0x1f0000 | PTE_P | PTE_W,
	0x1f1000 | PTE_P | PTE_W,
	0x1f2000 | PTE_P | PTE_W,
	0x1f3000 | PTE_P | PTE_W,
	0x1f4000 | PTE_P | PTE_W,
	0x1f5000 | PTE_P | PTE_W,
	0x1f6000 | PTE_P | PTE_W,
	0x1f7000 | PTE_P | PTE_W,
	0x1f8000 | PTE_P | PTE_W,
	0x1f9000 | PTE_P | PTE_W,
	0x1fa000 | PTE_P | PTE_W,
	0x1fb000 | PTE_P | PTE_W,
	0x1fc000 | PTE_P | PTE_W,
	0x1fd000 | PTE_P | PTE_W,
	0x1fe000 | PTE_P | PTE_W,
	0x1ff000 | PTE_P | PTE_W,
	0x200000 | PTE_P | PTE_W,
	0x201000 | PTE_P | PTE_W,
	0x202000 | PTE_P | PTE_W,
	0x203000 | PTE_P | PTE_W,
	0x204000 | PTE_P | PTE_W,
	0x205000 | PTE_P | PTE_W,
	0x206000 | PTE_P | PTE_W,
	0x207000 | PTE_P | PTE_W,
	0x208000 | PTE_P | PTE_W,
	0x209000 | PTE_P | PTE_W,
	0x20a000 | PTE_P | PTE_W,
	0x20b000 | PTE_P | PTE_W,
	0x20c000 | PTE_P | PTE_W,
	0x20d000 | PTE_P | PTE_W,
	0x20e000 | PTE_P | PTE_W,
	0x20f000 | PTE_P | PTE_W,
	0x210000 | PTE_P | PTE_W,
	0x211000 | PTE_P | PTE_W,
	0x212000 | PTE_P | PTE_W,
	0x213000 | PTE_P | PTE_W,
	0x214000 | PTE_P | PTE_W,
	0x215000 | PTE_P | PTE_W,
	0x216000 | PTE_P | PTE_W,
	0x217000 | PTE_P | PTE_W,
	0x218000 | PTE_P | PTE_W,
	0x219000 | PTE_P | PTE_W,
	0x21a000 | PTE_P | PTE_W,
	0x21b000 | PTE_P | PTE_W,
	0x21c000 | PTE_P | PTE_W,
	0x21d000 | PTE_P | PTE_W,
	0x21e000 | PTE_P | PTE_W,
	0x21f000 | PTE_P | PTE_W,
	0x220000 | PTE_P | PTE_W,
	0x221000 | PTE_P | PTE_W,
	0x222000 | PTE_P | PTE_W,
	0x223000 | PTE_P | PTE_W,
	0x224000 | PTE_P | PTE_W,
	0x225000 | PTE_P | PTE_W,
	0x226000 | PTE_P | PTE_W,
	0x227000 | PTE_P | PTE_W,
	0x228000 | PTE_P | PTE_W,
	0x229000 | PTE_P | PTE_W,
	0x22a000 | PTE_P | PTE_W,
	0x22b000 | PTE_P | PTE_W,
	0x22c000 | PTE_P | PTE_W,
	0x22d000 | PTE_P | PTE_W,
	0x22e000 | PTE_P | PTE_W,
	0x22f000 | PTE_P | PTE_W,
	0x230000 | PTE_P | PTE_W,
	0x231000 | PTE_P | PTE_W,
	0x232000 | PTE_P | PTE_W,
	0x233000 | PTE_P | PTE_W,
	0x234000 | PTE_P | PTE_W,
	0x235000 | PTE_P | PTE_W,
	0x236000 | PTE_P | PTE_W,
	0x237000 | PTE_P | PTE_W,
	0x238000 | PTE_P | PTE_W,
	0x239000 | PTE_P | PTE_W,
	0x23a000 | PTE_P | PTE_W,
	0x23b000 | PTE_P | PTE_W,
	0x23c000 | PTE_P | PTE_W,
	0x23d000 | PTE_P | PTE_W,
	0x23e000 | PTE_P | PTE_W,
	0x23f000 | PTE_P | PTE_W,
	0x240000 | PTE_P | PTE_W,
	0x241000 | PTE_P | PTE_W,
	0x242000 | PTE_P | PTE_W,
	0x243000 | PTE_P | PTE_W,
	0x244000 | PTE_P | PTE_W,
	0x245000 | PTE_P | PTE_W,
	0x246000 | PTE_P | PTE_W,
	0x247000 | PTE_P | PTE_W,
	0x248000 | PTE_P | PTE_W,
	0x249000 | PTE_P | PTE_W,
	0x24a000 | PTE_P | PTE_W,
	0x24b000 | PTE_P | PTE_W,
	0x24c000 | PTE_P | PTE_W,
	0x24d000 | PTE_P | PTE_W,
	0x24e000 | PTE_P | PTE_W,
	0x24f000 | PTE_P | PTE_W,
	0x250000 | PTE_P | PTE_W,
	0x251000 | PTE_P | PTE_W,
	0x252000 | PTE_P | PTE_W,
	0x253000 | PTE_P | PTE_W,
	0x254000 | PTE_P | PTE_W,
	0x255000 | PTE_P | PTE_W,
	0x256000 | PTE_P | PTE_W,
	0x257000 | PTE_P | PTE_W,
	0x258000 | PTE_P | PTE_W,
	0x259000 | PTE_P | PTE_W,
	0x25a000 | PTE_P | PTE_W,
	0x25b000 | PTE_P | PTE_W,
	0x25c000 | PTE_P | PTE_W,
	0x25d000 | PTE_P | PTE_W,
	0x25e000 | PTE_P | PTE_W,
	0x25f000 | PTE_P | PTE_W,
	0x260000 | PTE_P | PTE_W,
	0x261000 | PTE_P | PTE_W,
	0x262000 | PTE_P | PTE_W,
	0x263000 | PTE_P | PTE_W,
	0x264000 | PTE_P | PTE_W,
	0x265000 | PTE_P | PTE_W,
	0x266000 | PTE_P | PTE_W,
	0x267000 | PTE_P | PTE_W,
	0x268000 | PTE_P | PTE_W,
	0x269000 | PTE_P | PTE_W,
	0x26a000 | PTE_P | PTE_W,
	0x26b000 | PTE_P | PTE_W,
	0x26c000 | PTE_P | PTE_W,
	0x26d000 | PTE_P | PTE_W,
	0x26e000 | PTE_P | PTE_W,
	0x26f000 | PTE_P | PTE_W,
	0x270000 | PTE_P | PTE_W,
	0x271000 | PTE_P | PTE_W,
	0x272000 | PTE_P | PTE_W,
	0x273000 | PTE_P | PTE_W,
	0x274000 | PTE_P | PTE_W,
	0x275000 | PTE_P | PTE_W,
	0x276000 | PTE_P | PTE_W,
	0x277000 | PTE_P | PTE_W,
	0x278000 | PTE_P | PTE_W,
	0x279000 | PTE_P | PTE_W,
	0x27a000 | PTE_P | PTE_W,
	0x27b000 | PTE_P | PTE_W,
	0x27c000 | PTE_P | PTE_W,
	0x27d000 | PTE_P | PTE_W,
	0x27e000 | PTE_P | PTE_W,
	0x27f000 | PTE_P | PTE_W,
	0x280000 | PTE_P | PTE_W,
	0x281000 | PTE_P | PTE_W,
	0x282000 | PTE_P | PTE_W,
	0x283000 | PTE_P | PTE_W,
	0x284000 | PTE_P | PTE_W,
	0x285000 | PTE_P | PTE_W,
	0x286000 | PTE_P | PTE_W,
	0x287000 | PTE_P | PTE_W,
	0x288000 | PTE_P | PTE_W,
	0x289000 | PTE_P | PTE_W,
	0x28a000 | PTE_P | PTE_W,
	0x28b000 | PTE_P | PTE_W,
	0x28c000 | PTE_P | PTE_W,
	0x28d000 | PTE_P | PTE_W,
	0x28e000 | PTE_P | PTE_W,
	0x28f000 | PTE_P | PTE_W,
	0x290000 | PTE_P | PTE_W,
	0x291000 | PTE_P | PTE_W,
	0x292000 | PTE_P | PTE_W,
	0x293000 | PTE_P | PTE_W,
	0x294000 | PTE_P | PTE_W,
	0x295000 | PTE_P | PTE_W,
	0x296000 | PTE_P | PTE_W,
	0x297000 | PTE_P | PTE_W,
	0x298000 | PTE_P | PTE_W,
	0x299000 | PTE_P | PTE_W,
	0x29a000 | PTE_P | PTE_W,
	0x29b000 | PTE_P | PTE_W,
	0x29c000 | PTE_P | PTE_W,
	0x29d000 | PTE_P | PTE_W,
	0x29e000 | PTE_P | PTE_W,
	0x29f000 | PTE_P | PTE_W,
	0x2a0000 | PTE_P | PTE_W,
	0x2a1000 | PTE_P | PTE_W,
	0x2a2000 | PTE_P | PTE_W,
	0x2a3000 | PTE_P | PTE_W,
	0x2a4000 | PTE_P | PTE_W,
	0x2a5000 | PTE_P | PTE_W,
	0x2a6000 | PTE_P | PTE_W,
	0x2a7000 | PTE_P | PTE_W,
	0x2a8000 | PTE_P | PTE_W,
	0x2a9000 | PTE_P | PTE_W,
	0x2aa000 | PTE_P | PTE_W,
	0x2ab000 | PTE_P | PTE_W,
	0x2ac000 | PTE_P | PTE_W,
	0x2ad000 | PTE_P | PTE_W,
	0x2ae000 | PTE_P | PTE_W,
	0x2af000 | PTE_P | PTE_W,
	0x2b0000 | PTE_P | PTE_W,
	0x2b1000 | PTE_P | PTE_W,
	0x2b2000 | PTE_P | PTE_W,
	0x2b3000 | PTE_P | PTE_W,
	0x2b4000 | PTE_P | PTE_W,
	0x2b5000 | PTE_P | PTE_W,
	0x2b6000 | PTE_P | PTE_W,
	0x2b7000 | PTE_P | PTE_W,
	0x2b8000 | PTE_P | PTE_W,
	0x2b9000 | PTE_P | PTE_W,
	0x2ba000 | PTE_P | PTE_W,
	0x2bb000 | PTE_P | PTE_W,
	0x2bc000 | PTE_P | PTE_W,
	0x2bd000 | PTE_P | PTE_W,
	0x2be000 | PTE_P | PTE_W,
	0x2bf000 | PTE_P | PTE_W,
	0x2c0000 | PTE_P | PTE_W,
	0x2c1000 | PTE_P | PTE_W,
	0x2c2000 | PTE_P | PTE_W,
	0x2c3000 | PTE_P | PTE_W,
	0x2c4000 | PTE_P | PTE_W,
	0x2c5000 | PTE_P | PTE_W,
	0x2c6000 | PTE_P | PTE_W,
	0x2c7000 | PTE_P | PTE_W,
	0x2c8000 | PTE_P | PTE_W,
	0x2c9000 | PTE_P | PTE_W,
	0x2ca000 | PTE_P | PTE_W,
	0x2cb000 | PTE_P | PTE_W,
	0x2cc000 | PTE_P | PTE_W,
	0x2cd000 | PTE_P | PTE_W,
	0x2ce000 | PTE_P | PTE_W,
	0x2cf000 | PTE_P | PTE_W,
	0x2d0000 | PTE_P | PTE_W,
	0x2d1000 | PTE_P | PTE_W,
	0x2d2000 | PTE_P | PTE_W,
	0x2d3000 | PTE_P | PTE_W,
	0x2d4000 | PTE_P | PTE_W,
	0x2d5000 | PTE_P | PTE_W,
	0x2d6000 | PTE_P | PTE_W,
	0x2d7000 | PTE_P | PTE_W,
	0x2d8000 | PTE_P | PTE_W,
	0x2d9000 | PTE_P | PTE_W,
	0x2da000 | PTE_P | PTE_W,
	0x2db000 | PTE_P | PTE_W,
	0x2dc000 | PTE_P | PTE_W,
	0x2dd000 | PTE_P | PTE_W,
	0x2de000 | PTE_P | PTE_W,
	0x2df000 | PTE_P | PTE_W,
	0x2e0000 | PTE_P | PTE_W,
	0x2e1000 | PTE_P | PTE_W,
	0x2e2000 | PTE_P | PTE_W,
	0x2e3000 | PTE_P | PTE_W,
	0x2e4000 | PTE_P | PTE_W,
	0x2e5000 | PTE_P | PTE_W,
	0x2e6000 | PTE_P | PTE_W,
	0x2e7000 | PTE_P | PTE_W,
	0x2e8000 | PTE_P | PTE_W,
	0x2e9000 | PTE_P | PTE_W,
	0x2ea000 | PTE_P | PTE_W,
	0x2eb000 | PTE_P | PTE_W,
	0x2ec000 | PTE_P | PTE_W,
	0x2ed000 | PTE_P | PTE_W,
	0x2ee000 | PTE_P | PTE_W,
	0x2ef000 | PTE_P | PTE_W,
	0x2f0000 | PTE_P | PTE_W,
	0x2f1000 | PTE_P | PTE_W,
	0x2f2000 | PTE_P | PTE_W,
	0x2f3000 | PTE_P | PTE_W,
	0x2f4000 | PTE_P | PTE_W,
	0x2f5000 | PTE_P | PTE_W,
	0x2f6000 | PTE_P | PTE_W,
	0x2f7000 | PTE_P | PTE_W,
	0x2f8000 | PTE_P | PTE_W,
	0x2f9000 | PTE_P | PTE_W,
	0x2fa000 | PTE_P | PTE_W,
	0x2fb000 | PTE_P | PTE_W,
	0x2fc000 | PTE_P | PTE_W,
	0x2fd000 | PTE_P | PTE_W,
	0x2fe000 | PTE_P | PTE_W,
	0x2ff000 | PTE_P | PTE_W,
	0x300000 | PTE_P | PTE_W,
	0x301000 | PTE_P | PTE_W,
	0x302000 | PTE_P | PTE_W,
	0x303000 | PTE_P | PTE_W,
	0x304000 | PTE_P | PTE_W,
	0x305000 | PTE_P | PTE_W,
	0x306000 | PTE_P | PTE_W,
	0x307000 | PTE_P | PTE_W,
	0x308000 | PTE_P | PTE_W,
	0x309000 | PTE_P | PTE_W,
	0x30a000 | PTE_P | PTE_W,
	0x30b000 | PTE_P | PTE_W,
	0x30c000 | PTE_P | PTE_W,
	0x30d000 | PTE_P | PTE_W,
	0x30e000 | PTE_P | PTE_W,
	0x30f000 | PTE_P | PTE_W,
	0x310000 | PTE_P | PTE_W,
	0x311000 | PTE_P | PTE_W,
	0x312000 | PTE_P | PTE_W,
	0x313000 | PTE_P | PTE_W,
	0x314000 | PTE_P | PTE_W,
	0x315000 | PTE_P | PTE_W,
	0x316000 | PTE_P | PTE_W,
	0x317000 | PTE_P | PTE_W,
	0x318000 | PTE_P | PTE_W,
	0x319000 | PTE_P | PTE_W,
	0x31a000 | PTE_P | PTE_W,
	0x31b000 | PTE_P | PTE_W,
	0x31c000 | PTE_P | PTE_W,
	0x31d000 | PTE_P | PTE_W,
	0x31e000 | PTE_P | PTE_W,
	0x31f000 | PTE_P | PTE_W,
	0x320000 | PTE_P | PTE_W,
	0x321000 | PTE_P | PTE_W,
	0x322000 | PTE_P | PTE_W,
	0x323000 | PTE_P | PTE_W,
	0x324000 | PTE_P | PTE_W,
	0x325000 | PTE_P | PTE_W,
	0x326000 | PTE_P | PTE_W,
	0x327000 | PTE_P | PTE_W,
	0x328000 | PTE_P | PTE_W,
	0x329000 | PTE_P | PTE_W,
	0x32a000 | PTE_P | PTE_W,
	0x32b000 | PTE_P | PTE_W,
	0x32c000 | PTE_P | PTE_W,
	0x32d000 | PTE_P | PTE_W,
	0x32e000 | PTE_P | PTE_W,
	0x32f000 | PTE_P | PTE_W,
	0x330000 | PTE_P | PTE_W,
	0x331000 | PTE_P | PTE_W,
	0x332000 | PTE_P | PTE_W,
	0x333000 | PTE_P | PTE_W,
	0x334000 | PTE_P | PTE_W,
	0x335000 | PTE_P | PTE_W,
	0x336000 | PTE_P | PTE_W,
	0x337000 | PTE_P | PTE_W,
	0x338000 | PTE_P | PTE_W,
	0x339000 | PTE_P | PTE_W,
	0x33a000 | PTE_P | PTE_W,
	0x33b000 | PTE_P | PTE_W,
	0x33c000 | PTE_P | PTE_W,
	0x33d000 | PTE_P | PTE_W,
	0x33e000 | PTE_P | PTE_W,
	0x33f000 | PTE_P | PTE_W,
	0x340000 | PTE_P | PTE_W,
	0x341000 | PTE_P | PTE_W,
	0x342000 | PTE_P | PTE_W,
	0x343000 | PTE_P | PTE_W,
	0x344000 | PTE_P | PTE_W,
	0x345000 | PTE_P | PTE_W,
	0x346000 | PTE_P | PTE_W,
	0x347000 | PTE_P | PTE_W,
	0x348000 | PTE_P | PTE_W,
	0x349000 | PTE_P | PTE_W,
	0x34a000 | PTE_P | PTE_W,
	0x34b000 | PTE_P | PTE_W,
	0x34c000 | PTE_P | PTE_W,
	0x34d000 | PTE_P | PTE_W,
	0x34e000 | PTE_P | PTE_W,
	0x34f000 | PTE_P | PTE_W,
	0x350000 | PTE_P | PTE_W,
	0x351000 | PTE_P | PTE_W,
	0x352000 | PTE_P | PTE_W,
	0x353000 | PTE_P | PTE_W,
	0x354000 | PTE_P | PTE_W,
	0x355000 | PTE_P | PTE_W,
	0x356000 | PTE_P | PTE_W,
	0x357000 | PTE_P | PTE_W,
	0x358000 | PTE_P | PTE_W,
	0x359000 | PTE_P | PTE_W,
	0x35a000 | PTE_P | PTE_W,
	0x35b000 | PTE_P | PTE_W,
	0x35c000 | PTE_P | PTE_W,
	0x35d000 | PTE_P | PTE_W,
	0x35e000 | PTE_P | PTE_W,
	0x35f000 | PTE_P | PTE_W,
	0x360000 | PTE_P | PTE_W,
	0x361000 | PTE_P | PTE_W,
	0x362000 | PTE_P | PTE_W,
	0x363000 | PTE_P | PTE_W,
	0x364000 | PTE_P | PTE_W,
	0x365000 | PTE_P | PTE_W,
	0x366000 | PTE_P | PTE_W,
	0x367000 | PTE_P | PTE_W,
	0x368000 | PTE_P | PTE_W,
	0x369000 | PTE_P | PTE_W,
	0x36a000 | PTE_P | PTE_W,
	0x36b000 | PTE_P | PTE_W,
	0x36c000 | PTE_P | PTE_W,
	0x36d000 | PTE_P | PTE_W,
	0x36e000 | PTE_P | PTE_W,
	0x36f000 | PTE_P | PTE_W,
	0x370000 | PTE_P | PTE_W,
	0x371000 | PTE_P | PTE_W,
	0x372000 | PTE_P | PTE_W,
	0x373000 | PTE_P | PTE_W,
	0x374000 | PTE_P | PTE_W,
	0x375000 | PTE_P | PTE_W,
	0x376000 | PTE_P | PTE_W,
	0x377000 | PTE_P | PTE_W,
	0x378000 | PTE_P | PTE_W,
	0x379000 | PTE_P | PTE_W,
	0x37a000 | PTE_P | PTE_W,
	0x37b000 | PTE_P | PTE_W,
	0x37c000 | PTE_P | PTE_W,
	0x37d000 | PTE_P | PTE_W,
	0x37e000 | PTE_P | PTE_W,
	0x37f000 | PTE_P | PTE_W,
	0x380000 | PTE_P | PTE_W,
	0x381000 | PTE_P | PTE_W,
	0x382000 | PTE_P | PTE_W,
	0x383000 | PTE_P | PTE_W,
	0x384000 | PTE_P | PTE_W,
	0x385000 | PTE_P | PTE_W,
	0x386000 | PTE_P | PTE_W,
	0x387000 | PTE_P | PTE_W,
	0x388000 | PTE_P | PTE_W,
	0x389000 | PTE_P | PTE_W,
	0x38a000 | PTE_P | PTE_W,
	0x38b000 | PTE_P | PTE_W,
	0x38c000 | PTE_P | PTE_W,
	0x38d000 | PTE_P | PTE_W,
	0x38e000 | PTE_P | PTE_W,
	0x38f000 | PTE_P | PTE_W,
	0x390000 | PTE_P | PTE_W,
	0x391000 | PTE_P | PTE_W,
	0x392000 | PTE_P | PTE_W,
	0x393000 | PTE_P | PTE_W,
	0x394000 | PTE_P | PTE_W,
	0x395000 | PTE_P | PTE_W,
	0x396000 | PTE_P | PTE_W,
	0x397000 | PTE_P | PTE_W,
	0x398000 | PTE_P | PTE_W,
	0x399000 | PTE_P | PTE_W,
	0x39a000 | PTE_P | PTE_W,
	0x39b000 | PTE_P | PTE_W,
	0x39c000 | PTE_P | PTE_W,
	0x39d000 | PTE_P | PTE_W,
	0x39e000 | PTE_P | PTE_W,
	0x39f000 | PTE_P | PTE_W,
	0x3a0000 | PTE_P | PTE_W,
	0x3a1000 | PTE_P | PTE_W,
	0x3a2000 | PTE_P | PTE_W,
	0x3a3000 | PTE_P | PTE_W,
	0x3a4000 | PTE_P | PTE_W,
	0x3a5000 | PTE_P | PTE_W,
	0x3a6000 | PTE_P | PTE_W,
	0x3a7000 | PTE_P | PTE_W,
	0x3a8000 | PTE_P | PTE_W,
	0x3a9000 | PTE_P | PTE_W,
	0x3aa000 | PTE_P | PTE_W,
	0x3ab000 | PTE_P | PTE_W,
	0x3ac000 | PTE_P | PTE_W,
	0x3ad000 | PTE_P | PTE_W,
	0x3ae000 | PTE_P | PTE_W,
	0x3af000 | PTE_P | PTE_W,
	0x3b0000 | PTE_P | PTE_W,
	0x3b1000 | PTE_P | PTE_W,
	0x3b2000 | PTE_P | PTE_W,
	0x3b3000 | PTE_P | PTE_W,
	0x3b4000 | PTE_P | PTE_W,
	0x3b5000 | PTE_P | PTE_W,
	0x3b6000 | PTE_P | PTE_W,
	0x3b7000 | PTE_P | PTE_W,
	0x3b8000 | PTE_P | PTE_W,
	0x3b9000 | PTE_P | PTE_W,
	0x3ba000 | PTE_P | PTE_W,
	0x3bb000 | PTE_P | PTE_W,
	0x3bc000 | PTE_P | PTE_W,
	0x3bd000 | PTE_P | PTE_W,
	0x3be000 | PTE_P | PTE_W,
	0x3bf000 | PTE_P | PTE_W,
	0x3c0000 | PTE_P | PTE_W,
	0x3c1000 | PTE_P | PTE_W,
	0x3c2000 | PTE_P | PTE_W,
	0x3c3000 | PTE_P | PTE_W,
	0x3c4000 | PTE_P | PTE_W,
	0x3c5000 | PTE_P | PTE_W,
	0x3c6000 | PTE_P | PTE_W,
	0x3c7000 | PTE_P | PTE_W,
	0x3c8000 | PTE_P | PTE_W,
	0x3c9000 | PTE_P | PTE_W,
	0x3ca000 | PTE_P | PTE_W,
	0x3cb000 | PTE_P | PTE_W,
	0x3cc000 | PTE_P | PTE_W,
	0x3cd000 | PTE_P | PTE_W,
	0x3ce000 | PTE_P | PTE_W,
	0x3cf000 | PTE_P | PTE_W,
	0x3d0000 | PTE_P | PTE_W,
	0x3d1000 | PTE_P | PTE_W,
	0x3d2000 | PTE_P | PTE_W,
	0x3d3000 | PTE_P | PTE_W,
	0x3d4000 | PTE_P | PTE_W,
	0x3d5000 | PTE_P | PTE_W,
	0x3d6000 | PTE_P | PTE_W,
	0x3d7000 | PTE_P | PTE_W,
	0x3d8000 | PTE_P | PTE_W,
	0x3d9000 | PTE_P | PTE_W,
	0x3da000 | PTE_P | PTE_W,
	0x3db000 | PTE_P | PTE_W,
	0x3dc000 | PTE_P | PTE_W,
	0x3dd000 | PTE_P | PTE_W,
	0x3de000 | PTE_P | PTE_W,
	0x3df000 | PTE_P | PTE_W,
	0x3e0000 | PTE_P | PTE_W,
	0x3e1000 | PTE_P | PTE_W,
	0x3e2000 | PTE_P | PTE_W,
	0x3e3000 | PTE_P | PTE_W,
	0x3e4000 | PTE_P | PTE_W,
	0x3e5000 | PTE_P | PTE_W,
	0x3e6000 | PTE_P | PTE_W,
	0x3e7000 | PTE_P | PTE_W,
	0x3e8000 | PTE_P | PTE_W,
	0x3e9000 | PTE_P | PTE_W,
	0x3ea000 | PTE_P | PTE_W,
	0x3eb000 | PTE_P | PTE_W,
	0x3ec000 | PTE_P | PTE_W,
	0x3ed000 | PTE_P | PTE_W,
	0x3ee000 | PTE_P | PTE_W,
	0x3ef000 | PTE_P | PTE_W,
	0x3f0000 | PTE_P | PTE_W,
	0x3f1000 | PTE_P | PTE_W,
	0x3f2000 | PTE_P | PTE_W,
	0x3f3000 | PTE_P | PTE_W,
	0x3f4000 | PTE_P | PTE_W,
	0x3f5000 | PTE_P | PTE_W,
	0x3f6000 | PTE_P | PTE_W,
	0x3f7000 | PTE_P | PTE_W,
	0x3f8000 | PTE_P | PTE_W,
	0x3f9000 | PTE_P | PTE_W,
	0x3fa000 | PTE_P | PTE_W,
	0x3fb000 | PTE_P | PTE_W,
	0x3fc000 | PTE_P | PTE_W,
	0x3fd000 | PTE_P | PTE_W,
	0x3fe000 | PTE_P | PTE_W,
	0x3ff000 | PTE_P | PTE_W,
};


```
####init.c
```
/* See COPYRIGHT for copyright information. */

#include <inc/stdio.h>
#include <inc/string.h>
#include <inc/assert.h>

#include <kern/monitor.h>
#include <kern/console.h>

// Test the stack backtrace function (lab 1 only)
void
test_backtrace(int x)
{
	cprintf("entering test_backtrace %d\n", x);
	if (x > 0)
		test_backtrace(x-1);
	else
		mon_backtrace(0, 0, 0);
	cprintf("leaving test_backtrace %d\n", x);
}

void
i386_init(void)
{
	extern char edata[], end[];

	// Before doing anything else, complete the ELF loading process.
	// Clear the uninitialized global data (BSS) section of our program.
	// This ensures that all static/global variables start out zero.
	memset(edata, 0, end - edata);

	// Initialize the console.
	// Can't call cprintf until after we do this!
	cons_init();

	cprintf("6828 decimal is %o octal!\n", 6828);

	// Test the stack backtrace function (lab 1 only)
	test_backtrace(5);

	// Drop into the kernel monitor.
	while (1)
		monitor(NULL);
}


/*
 * Variable panicstr contains argument to first call to panic; used as flag
 * to indicate that the kernel has already called panic.
 */
const char *panicstr;

/*
 * Panic is called on unresolvable fatal errors.
 * It prints "panic: mesg", and then enters the kernel monitor.
 */
void
_panic(const char *file, int line, const char *fmt,...)
{
	va_list ap;

	if (panicstr)
		goto dead;
	panicstr = fmt;

	// Be extra sure that the machine is in as reasonable state
	asm volatile("cli; cld");

	va_start(ap, fmt);
	cprintf("kernel panic at %s:%d: ", file, line);
	vcprintf(fmt, ap);
	cprintf("\n");
	va_end(ap);

dead:
	/* break into the kernel monitor */
	while (1)
		monitor(NULL);
}

/* like panic, but don't */
void
_warn(const char *file, int line, const char *fmt,...)
{
	va_list ap;

	va_start(ap, fmt);
	cprintf("kernel warning at %s:%d: ", file, line);
	vcprintf(fmt, ap);
	cprintf("\n");
	va_end(ap);
}

```
####console.c
```
/* See COPYRIGHT for copyright information. */

#include <inc/x86.h>
#include <inc/memlayout.h>
#include <inc/kbdreg.h>
#include <inc/string.h>
#include <inc/assert.h>

#include <kern/console.h>

static void cons_intr(int (*proc)(void));
static void cons_putc(int c);

// Stupid I/O delay routine necessitated by historical PC design flaws
static void
delay(void)
{
	inb(0x84);
	inb(0x84);
	inb(0x84);
	inb(0x84);
}

/***** Serial I/O code *****/

#define COM1		0x3F8

#define COM_RX		0	// In:	Receive buffer (DLAB=0)
#define COM_TX		0	// Out: Transmit buffer (DLAB=0)
#define COM_DLL		0	// Out: Divisor Latch Low (DLAB=1)
#define COM_DLM		1	// Out: Divisor Latch High (DLAB=1)
#define COM_IER		1	// Out: Interrupt Enable Register
#define   COM_IER_RDI	0x01	//   Enable receiver data interrupt
#define COM_IIR		2	// In:	Interrupt ID Register
#define COM_FCR		2	// Out: FIFO Control Register
#define COM_LCR		3	// Out: Line Control Register
#define	  COM_LCR_DLAB	0x80	//   Divisor latch access bit
#define	  COM_LCR_WLEN8	0x03	//   Wordlength: 8 bits
#define COM_MCR		4	// Out: Modem Control Register
#define	  COM_MCR_RTS	0x02	// RTS complement
#define	  COM_MCR_DTR	0x01	// DTR complement
#define	  COM_MCR_OUT2	0x08	// Out2 complement
#define COM_LSR		5	// In:	Line Status Register
#define   COM_LSR_DATA	0x01	//   Data available
#define   COM_LSR_TXRDY	0x20	//   Transmit buffer avail
#define   COM_LSR_TSRE	0x40	//   Transmitter off

static bool serial_exists;

static int
serial_proc_data(void)
{
	if (!(inb(COM1+COM_LSR) & COM_LSR_DATA))
		return -1;
	return inb(COM1+COM_RX);
}

void
serial_intr(void)
{
	if (serial_exists)
		cons_intr(serial_proc_data);
}

static void
serial_putc(int c)
{
	int i;

	for (i = 0;
	     !(inb(COM1 + COM_LSR) & COM_LSR_TXRDY) && i < 12800;
	     i++)
		delay();

	outb(COM1 + COM_TX, c);
}

static void
serial_init(void)
{
	// Turn off the FIFO
	outb(COM1+COM_FCR, 0);

	// Set speed; requires DLAB latch
	outb(COM1+COM_LCR, COM_LCR_DLAB);
	outb(COM1+COM_DLL, (uint8_t) (115200 / 9600));
	outb(COM1+COM_DLM, 0);

	// 8 data bits, 1 stop bit, parity off; turn off DLAB latch
	outb(COM1+COM_LCR, COM_LCR_WLEN8 & ~COM_LCR_DLAB);

	// No modem controls
	outb(COM1+COM_MCR, 0);
	// Enable rcv interrupts
	outb(COM1+COM_IER, COM_IER_RDI);

	// Clear any preexisting overrun indications and interrupts
	// Serial port doesn't exist if COM_LSR returns 0xFF
	serial_exists = (inb(COM1+COM_LSR) != 0xFF);
	(void) inb(COM1+COM_IIR);
	(void) inb(COM1+COM_RX);

}



/***** Parallel port output code *****/
// For information on PC parallel port programming, see the class References
// page.

static void
lpt_putc(int c)
{
	int i;

	for (i = 0; !(inb(0x378+1) & 0x80) && i < 12800; i++)
		delay();
	outb(0x378+0, c);
	outb(0x378+2, 0x08|0x04|0x01);
	outb(0x378+2, 0x08);
}




/***** Text-mode CGA/VGA display output *****/

static unsigned addr_6845;
static uint16_t *crt_buf;
static uint16_t crt_pos;

static void
cga_init(void)
{
	volatile uint16_t *cp;
	uint16_t was;
	unsigned pos;

	cp = (uint16_t*) (KERNBASE + CGA_BUF);
	was = *cp;
	*cp = (uint16_t) 0xA55A;
	if (*cp != 0xA55A) {
		cp = (uint16_t*) (KERNBASE + MONO_BUF);
		addr_6845 = MONO_BASE;
	} else {
		*cp = was;
		addr_6845 = CGA_BASE;
	}

	/* Extract cursor location */
	outb(addr_6845, 14);
	pos = inb(addr_6845 + 1) << 8;
	outb(addr_6845, 15);
	pos |= inb(addr_6845 + 1);

	crt_buf = (uint16_t*) cp;
	crt_pos = pos;
}



static void
cga_putc(int c)
{
	// if no attribute given, then use black on white
	if (!(c & ~0xFF))
		c |= 0x0700;

	switch (c & 0xff) {
	case '\b':
		if (crt_pos > 0) {
			crt_pos--;
			crt_buf[crt_pos] = (c & ~0xff) | ' ';
		}
		break;
	case '\n':
		crt_pos += CRT_COLS;
		/* fallthru */
	case '\r':
		crt_pos -= (crt_pos % CRT_COLS);
		break;
	case '\t':
		cons_putc(' ');
		cons_putc(' ');
		cons_putc(' ');
		cons_putc(' ');
		cons_putc(' ');
		break;
	default:
		crt_buf[crt_pos++] = c;		/* write the character */
		break;
	}

	// What is the purpose of this?
	
//	if(crt_pos >= 10){
//		crt_pos = 0;
//	}
	
		
	
	if (crt_pos >= CRT_SIZE) {

		int i;

		memmove(crt_buf, crt_buf + CRT_COLS, (CRT_SIZE - CRT_COLS) * sizeof(uint16_t));
		for (i = CRT_SIZE - CRT_COLS; i < CRT_SIZE; i++)
			crt_buf[i] = 0x0700 | ' ';
		crt_pos -= CRT_COLS;
				cons_putc(' ');
		cons_putc('T');		
		cons_putc('T');
		cons_putc('T');		
		cons_putc('T');
		cons_putc('T');		
		cons_putc('T');
		cons_putc(' ');
			


	}

	
	/* move that little blinky thing */
	outb(addr_6845, 14);
	outb(addr_6845 + 1, crt_pos >> 8);
	outb(addr_6845, 15);
	outb(addr_6845 + 1, crt_pos);
}


/***** Keyboard input code *****/

#define NO		0

#define SHIFT		(1<<0)
#define CTL		(1<<1)
#define ALT		(1<<2)

#define CAPSLOCK	(1<<3)
#define NUMLOCK		(1<<4)
#define SCROLLLOCK	(1<<5)

#define E0ESC		(1<<6)

static uint8_t shiftcode[256] =
{
	[0x1D] = CTL,
	[0x2A] = SHIFT,
	[0x36] = SHIFT,
	[0x38] = ALT,
	[0x9D] = CTL,
	[0xB8] = ALT
};

static uint8_t togglecode[256] =
{
	[0x3A] = CAPSLOCK,
	[0x45] = NUMLOCK,
	[0x46] = SCROLLLOCK
};

static uint8_t normalmap[256] =
{
	NO,   0x1B, '1',  '2',  '3',  '4',  '5',  '6',	// 0x00
	'7',  '8',  '9',  '0',  '-',  '=',  '\b', '\t',
	'q',  'w',  'e',  'r',  't',  'y',  'u',  'i',	// 0x10
	'o',  'p',  '[',  ']',  '\n', NO,   'a',  's',
	'd',  'f',  'g',  'h',  'j',  'k',  'l',  ';',	// 0x20
	'\'', '`',  NO,   '\\', 'z',  'x',  'c',  'v',
	'b',  'n',  'm',  ',',  '.',  '/',  NO,   '*',	// 0x30
	NO,   ' ',  NO,   NO,   NO,   NO,   NO,   NO,
	NO,   NO,   NO,   NO,   NO,   NO,   NO,   '7',	// 0x40
	'8',  '9',  '-',  '4',  '5',  '6',  '+',  '1',
	'2',  '3',  '0',  '.',  NO,   NO,   NO,   NO,	// 0x50
	[0xC7] = KEY_HOME,	      [0x9C] = '\n' /*KP_Enter*/,
	[0xB5] = '/' /*KP_Div*/,      [0xC8] = KEY_UP,
	[0xC9] = KEY_PGUP,	      [0xCB] = KEY_LF,
	[0xCD] = KEY_RT,	      [0xCF] = KEY_END,
	[0xD0] = KEY_DN,	      [0xD1] = KEY_PGDN,
	[0xD2] = KEY_INS,	      [0xD3] = KEY_DEL
};

static uint8_t shiftmap[256] =
{
	NO,   033,  '!',  '@',  '#',  '$',  '%',  '^',	// 0x00
	'&',  '*',  '(',  ')',  '_',  '+',  '\b', '\t',
	'Q',  'W',  'E',  'R',  'T',  'Y',  'U',  'I',	// 0x10
	'O',  'P',  '{',  '}',  '\n', NO,   'A',  'S',
	'D',  'F',  'G',  'H',  'J',  'K',  'L',  ':',	// 0x20
	'"',  '~',  NO,   '|',  'Z',  'X',  'C',  'V',
	'B',  'N',  'M',  '<',  '>',  '?',  NO,   '*',	// 0x30
	NO,   ' ',  NO,   NO,   NO,   NO,   NO,   NO,
	NO,   NO,   NO,   NO,   NO,   NO,   NO,   '7',	// 0x40
	'8',  '9',  '-',  '4',  '5',  '6',  '+',  '1',
	'2',  '3',  '0',  '.',  NO,   NO,   NO,   NO,	// 0x50
	[0xC7] = KEY_HOME,	      [0x9C] = '\n' /*KP_Enter*/,
	[0xB5] = '/' /*KP_Div*/,      [0xC8] = KEY_UP,
	[0xC9] = KEY_PGUP,	      [0xCB] = KEY_LF,
	[0xCD] = KEY_RT,	      [0xCF] = KEY_END,
	[0xD0] = KEY_DN,	      [0xD1] = KEY_PGDN,
	[0xD2] = KEY_INS,	      [0xD3] = KEY_DEL
};

#define C(x) (x - '@')

static uint8_t ctlmap[256] =
{
	NO,      NO,      NO,      NO,      NO,      NO,      NO,      NO,
	NO,      NO,      NO,      NO,      NO,      NO,      NO,      NO,
	C('Q'),  C('W'),  C('E'),  C('R'),  C('T'),  C('Y'),  C('U'),  C('I'),
	C('O'),  C('P'),  NO,      NO,      '\r',    NO,      C('A'),  C('S'),
	C('D'),  C('F'),  C('G'),  C('H'),  C('J'),  C('K'),  C('L'),  NO,
	NO,      NO,      NO,      C('\\'), C('Z'),  C('X'),  C('C'),  C('V'),
	C('B'),  C('N'),  C('M'),  NO,      NO,      C('/'),  NO,      NO,
	[0x97] = KEY_HOME,
	[0xB5] = C('/'),		[0xC8] = KEY_UP,
	[0xC9] = KEY_PGUP,		[0xCB] = KEY_LF,
	[0xCD] = KEY_RT,		[0xCF] = KEY_END,
	[0xD0] = KEY_DN,		[0xD1] = KEY_PGDN,
	[0xD2] = KEY_INS,		[0xD3] = KEY_DEL
};

static uint8_t *charcode[4] = {
	normalmap,
	shiftmap,
	ctlmap,
	ctlmap
};

/*
 * Get data from the keyboard.  If we finish a character, return it.  Else 0.
 * Return -1 if no data.
 */
static int
kbd_proc_data(void)
{
	int c;
	uint8_t stat, data;
	static uint32_t shift;

	stat = inb(KBSTATP);
	if ((stat & KBS_DIB) == 0)
		return -1;
	// Ignore data from mouse.
	if (stat & KBS_TERR)
		return -1;

	data = inb(KBDATAP);

	if (data == 0xE0) {
		// E0 escape character
		shift |= E0ESC;
		return 0;
	} else if (data & 0x80) {
		// Key released
		data = (shift & E0ESC ? data : data & 0x7F);
		shift &= ~(shiftcode[data] | E0ESC);
		return 0;
	} else if (shift & E0ESC) {
		// Last character was an E0 escape; or with 0x80
		data |= 0x80;
		shift &= ~E0ESC;
	}

	shift |= shiftcode[data];
	shift ^= togglecode[data];

	c = charcode[shift & (CTL | SHIFT)][data];
	if (shift & CAPSLOCK) {
		if ('a' <= c && c <= 'z')
			c += 'A' - 'a';
		else if ('A' <= c && c <= 'Z')
			c += 'a' - 'A';
	}

	// Process special keys
	// Ctrl-Alt-Del: reboot
	if (!(~shift & (CTL | ALT)) && c == KEY_DEL) {
		cprintf("Rebooting!\n");
		outb(0x92, 0x3); // courtesy of Chris Frost
	}

	return c;
}

void
kbd_intr(void)
{
	cons_intr(kbd_proc_data);
}

static void
kbd_init(void)
{
}



/***** General device-independent console code *****/
// Here we manage the console input buffer,
// where we stash characters received from the keyboard or serial port
// whenever the corresponding interrupt occurs.

#define CONSBUFSIZE 512

static struct {
	uint8_t buf[CONSBUFSIZE];
	uint32_t rpos;
	uint32_t wpos;
} cons;

// called by device interrupt routines to feed input characters
// into the circular console input buffer.
static void
cons_intr(int (*proc)(void))
{
	int c;

	while ((c = (*proc)()) != -1) {
		if (c == 0)
			continue;
		cons.buf[cons.wpos++] = c;
		if (cons.wpos == CONSBUFSIZE)
			cons.wpos = 0;
	}
}

// return the next input character from the console, or 0 if none waiting
int
cons_getc(void)
{
	int c;

	// poll for any pending input characters,
	// so that this function works even when interrupts are disabled
	// (e.g., when called from the kernel monitor).
	serial_intr();
	kbd_intr();

	// grab the next character from the input buffer.
	if (cons.rpos != cons.wpos) {
		c = cons.buf[cons.rpos++];
		if (cons.rpos == CONSBUFSIZE)
			cons.rpos = 0;
		return c;
	}
	return 0;
}

// output a character to the console
static void
cons_putc(int c)
{
	serial_putc(c);
	lpt_putc(c);
	cga_putc(c);
}

// initialize the console devices
void
cons_init(void)
{
	cga_init();
	kbd_init();
	serial_init();

	if (!serial_exists)
		cprintf("Serial port does not exist!\n");
}


// `High'-level console I/O.  Used by readline and cprintf.

void
cputchar(int c)
{
	cons_putc(c);
}

int
getchar(void)
{
	int c;

	while ((c = cons_getc()) == 0)
		/* do nothing */;
	return c;
}

int
iscons(int fdnum)
{
	// used by readline
	return 1;
}

```

####monitor.c
```
// Simple command-line kernel monitor useful for
// controlling the kernel and exploring the system interactively.

#include <inc/stdio.h>
#include <inc/string.h>
#include <inc/memlayout.h>
#include <inc/assert.h>
#include <inc/x86.h>

#include <kern/console.h>
#include <kern/monitor.h>
#include <kern/kdebug.h>

#define CMDBUF_SIZE	80	// enough for one VGA text line


struct Command {
	const char *name;
	const char *desc;
	// return -1 to force monitor to exit
	int (*func)(int argc, char** argv, struct Trapframe* tf);
};

static struct Command commands[] = {
	{ "help", "Display this list of commands", mon_help },
	{ "kerninfo", "Display information about the kernel", mon_kerninfo },
};

/***** Implementations of basic kernel monitor commands *****/

int
mon_help(int argc, char **argv, struct Trapframe *tf)
{
	int i;

	for (i = 0; i < ARRAY_SIZE(commands); i++)
		cprintf("%s - %s\n", commands[i].name, commands[i].desc);
	return 0;
}

int
mon_kerninfo(int argc, char **argv, struct Trapframe *tf)
{
	extern char _start[], entry[], etext[], edata[], end[];

	cprintf("Special kernel symbols:\n");
	cprintf("  _start                  %08x (phys)\n", _start);
	cprintf("  entry  %08x (virt)  %08x (phys)\n", entry, entry - KERNBASE);
	cprintf("  etext  %08x (virt)  %08x (phys)\n", etext, etext - KERNBASE);
	cprintf("  edata  %08x (virt)  %08x (phys)\n", edata, edata - KERNBASE);
	cprintf("  end    %08x (virt)  %08x (phys)\n", end, end - KERNBASE);
	cprintf("Kernel executable memory footprint: %dKB\n",
		ROUNDUP(end - entry, 1024) / 1024);
	return 0;
}

int
mon_backtrace(int argc, char **argv, struct Trapframe *tf)
{
	// Your code here.
	return 0;
}



/***** Kernel monitor command interpreter *****/

#define WHITESPACE "\t\r\n "
#define MAXARGS 16

static int
runcmd(char *buf, struct Trapframe *tf)
{
	int argc;
	char *argv[MAXARGS];
	int i;

	// Parse the command buffer into whitespace-separated arguments
	argc = 0;
	argv[argc] = 0;
	while (1) {
		// gobble whitespace
		while (*buf && strchr(WHITESPACE, *buf))
			*buf++ = 0;
		if (*buf == 0)
			break;

		// save and scan past next arg
		if (argc == MAXARGS-1) {
			cprintf("Too many arguments (max %d)\n", MAXARGS);
			return 0;
		}
		argv[argc++] = buf;
		while (*buf && !strchr(WHITESPACE, *buf))
			buf++;
	}
	argv[argc] = 0;

	// Lookup and invoke the command
	if (argc == 0)
		return 0;
	for (i = 0; i < ARRAY_SIZE(commands); i++) {
		if (strcmp(argv[0], commands[i].name) == 0)
			return commands[i].func(argc, argv, tf);
	}
	cprintf("Unknown command '%s'\n", argv[0]);
	return 0;
}

void
monitor(struct Trapframe *tf)
{
	char *buf;

	cprintf("Welcome to the JOS kernel monitor!\n");
	cprintf("Type 'help' for a list of commands.\n");
	//my code here 
	cprintf("here show code of myself\n");
	//test the crt_pos CRT_SIZE the relationship
	for(int i = 0;i<500;i++){
		cprintf("%d",i);
		cprintf("abcdefghijklmnopqrstuvwxyz0123456789  \t");
	}
	//my code end
	while (1) {
		buf = readline("K> ");
		if (buf != NULL)
			if (runcmd(buf, tf) < 0)
				break;
	}
}

```
####kdebug.c
```
#include <inc/stab.h>
#include <inc/string.h>
#include <inc/memlayout.h>
#include <inc/assert.h>

#include <kern/kdebug.h>

extern const struct Stab __STAB_BEGIN__[];	// Beginning of stabs table
extern const struct Stab __STAB_END__[];	// End of stabs table
extern const char __STABSTR_BEGIN__[];		// Beginning of string table
extern const char __STABSTR_END__[];		// End of string table


// stab_binsearch(stabs, region_left, region_right, type, addr)
//
//	Some stab types are arranged in increasing order by instruction
//	address.  For example, N_FUN stabs (stab entries with n_type ==
//	N_FUN), which mark functions, and N_SO stabs, which mark source files.
//
//	Given an instruction address, this function finds the single stab
//	entry of type 'type' that contains that address.
//
//	The search takes place within the range [*region_left, *region_right].
//	Thus, to search an entire set of N stabs, you might do:
//
//		left = 0;
//		right = N - 1;     /* rightmost stab */
//		stab_binsearch(stabs, &left, &right, type, addr);
//
//	The search modifies *region_left and *region_right to bracket the
//	'addr'.  *region_left points to the matching stab that contains
//	'addr', and *region_right points just before the next stab.  If
//	*region_left > *region_right, then 'addr' is not contained in any
//	matching stab.
//
//	For example, given these N_SO stabs:
//		Index  Type   Address
//		0      SO     f0100000
//		13     SO     f0100040
//		117    SO     f0100176
//		118    SO     f0100178
//		555    SO     f0100652
//		556    SO     f0100654
//		657    SO     f0100849
//	this code:
//		left = 0, right = 657;
//		stab_binsearch(stabs, &left, &right, N_SO, 0xf0100184);
//	will exit setting left = 118, right = 554.
//
static void
stab_binsearch(const struct Stab *stabs, int *region_left, int *region_right,
	       int type, uintptr_t addr)
{
	int l = *region_left, r = *region_right, any_matches = 0;

	while (l <= r) {
		int true_m = (l + r) / 2, m = true_m;

		// search for earliest stab with right type
		while (m >= l && stabs[m].n_type != type)
			m--;
		if (m < l) {	// no match in [l, m]
			l = true_m + 1;
			continue;
		}

		// actual binary search
		any_matches = 1;
		if (stabs[m].n_value < addr) {
			*region_left = m;
			l = true_m + 1;
		} else if (stabs[m].n_value > addr) {
			*region_right = m - 1;
			r = m - 1;
		} else {
			// exact match for 'addr', but continue loop to find
			// *region_right
			*region_left = m;
			l = m;
			addr++;
		}
	}

	if (!any_matches)
		*region_right = *region_left - 1;
	else {
		// find rightmost region containing 'addr'
		for (l = *region_right;
		     l > *region_left && stabs[l].n_type != type;
		     l--)
			/* do nothing */;
		*region_left = l;
	}
}


// debuginfo_eip(addr, info)
//
//	Fill in the 'info' structure with information about the specified
//	instruction address, 'addr'.  Returns 0 if information was found, and
//	negative if not.  But even if it returns negative it has stored some
//	information into '*info'.
//
int
debuginfo_eip(uintptr_t addr, struct Eipdebuginfo *info)
{
	const struct Stab *stabs, *stab_end;
	const char *stabstr, *stabstr_end;
	int lfile, rfile, lfun, rfun, lline, rline;

	// Initialize *info
	info->eip_file = "<unknown>";
	info->eip_line = 0;
	info->eip_fn_name = "<unknown>";
	info->eip_fn_namelen = 9;
	info->eip_fn_addr = addr;
	info->eip_fn_narg = 0;

	// Find the relevant set of stabs
	if (addr >= ULIM) {
		stabs = __STAB_BEGIN__;
		stab_end = __STAB_END__;
		stabstr = __STABSTR_BEGIN__;
		stabstr_end = __STABSTR_END__;
	} else {
		// Can't search for user-level addresses yet!
  	        panic("User address");
	}

	// String table validity checks
	if (stabstr_end <= stabstr || stabstr_end[-1] != 0)
		return -1;

	// Now we find the right stabs that define the function containing
	// 'eip'.  First, we find the basic source file containing 'eip'.
	// Then, we look in that source file for the function.  Then we look
	// for the line number.

	// Search the entire set of stabs for the source file (type N_SO).
	lfile = 0;
	rfile = (stab_end - stabs) - 1;
	stab_binsearch(stabs, &lfile, &rfile, N_SO, addr);
	if (lfile == 0)
		return -1;

	// Search within that file's stabs for the function definition
	// (N_FUN).
	lfun = lfile;
	rfun = rfile;
	stab_binsearch(stabs, &lfun, &rfun, N_FUN, addr);

	if (lfun <= rfun) {
		// stabs[lfun] points to the function name
		// in the string table, but check bounds just in case.
		if (stabs[lfun].n_strx < stabstr_end - stabstr)
			info->eip_fn_name = stabstr + stabs[lfun].n_strx;
		info->eip_fn_addr = stabs[lfun].n_value;
		addr -= info->eip_fn_addr;
		// Search within the function definition for the line number.
		lline = lfun;
		rline = rfun;
	} else {
		// Couldn't find function stab!  Maybe we're in an assembly
		// file.  Search the whole file for the line number.
		info->eip_fn_addr = addr;
		lline = lfile;
		rline = rfile;
	}
	// Ignore stuff after the colon.
	info->eip_fn_namelen = strfind(info->eip_fn_name, ':') - info->eip_fn_name;


	// Search within [lline, rline] for the line number stab.
	// If found, set info->eip_line to the right line number.
	// If not found, return -1.
	//
	// Hint:
	//	There's a particular stabs type used for line numbers.
	//	Look at the STABS documentation and <inc/stab.h> to find
	//	which one.
	// Your code here.


	// Search backwards from the line number for the relevant filename
	// stab.
	// We can't just use the "lfile" stab because inlined functions
	// can interpolate code from a different file!
	// Such included source files use the N_SOL stab type.
	while (lline >= lfile
	       && stabs[lline].n_type != N_SOL
	       && (stabs[lline].n_type != N_SO || !stabs[lline].n_value))
		lline--;
	if (lline >= lfile && stabs[lline].n_strx < stabstr_end - stabstr)
		info->eip_file = stabstr + stabs[lline].n_strx;


	// Set eip_fn_narg to the number of arguments taken by the function,
	// or 0 if there was no containing function.
	if (lfun < rfun)
		for (lline = lfun + 1;
		     lline < rfun && stabs[lline].n_type == N_PSYM;
		     lline++)
			info->eip_fn_narg++;

	return 0;
}

```
####printf.c
```
// Simple implementation of cprintf console output for the kernel,
// based on printfmt() and the kernel console's cputchar().

#include <inc/types.h>
#include <inc/stdio.h>
#include <inc/stdarg.h>


static void
putch(int ch, int *cnt)
{
	cputchar(ch);
	*cnt++;
}

int
vcprintf(const char *fmt, va_list ap)
{
	int cnt = 0;

	vprintfmt((void*)putch, &cnt, fmt, ap);
	return cnt;
}

int
cprintf(const char *fmt, ...)
{
	va_list ap;
	int cnt;

	va_start(ap, fmt);
	cnt = vcprintf(fmt, ap);
	va_end(ap);

	return cnt;
}


```
###printfmt.c
```
// Stripped-down primitive printf-style formatting routines,
// used in common by printf, sprintf, fprintf, etc.
// This code is also used by both the kernel and user programs.

#include <inc/types.h>
#include <inc/stdio.h>
#include <inc/string.h>
#include <inc/stdarg.h>
#include <inc/error.h>

/*
 * Space or zero padding and a field width are supported for the numeric
 * formats only.
 *
 * The special format %e takes an integer error code
 * and prints a string describing the error.
 * The integer may be positive or negative,
 * so that -E_NO_MEM and E_NO_MEM are equivalent.
 */

static const char * const error_string[MAXERROR] =
{
	[E_UNSPECIFIED]	= "unspecified error",
	[E_BAD_ENV]	= "bad environment",
	[E_INVAL]	= "invalid parameter",
	[E_NO_MEM]	= "out of memory",
	[E_NO_FREE_ENV]	= "out of environments",
	[E_FAULT]	= "segmentation fault",
};

/*
 * Print a number (base <= 16) in reverse order,
 * using specified putch function and associated pointer putdat.
 */
static void
printnum(void (*putch)(int, void*), void *putdat,
	 unsigned long long num, unsigned base, int width, int padc)
{
	// first recursively print all preceding (more significant) digits
	if (num >= base) {
		printnum(putch, putdat, num / base, base, width - 1, padc);
	} else {
		// print any needed pad characters before first digit
		while (--width > 0)
			putch(padc, putdat);
	}

	// then print this (the least significant) digit
	putch("0123456789abcdef"[num % base], putdat);
}

// Get an unsigned int of various possible sizes from a varargs list,
// depending on the lflag parameter.
static unsigned long long
getuint(va_list *ap, int lflag)
{
	if (lflag >= 2)
		return va_arg(*ap, unsigned long long);
	else if (lflag)
		return va_arg(*ap, unsigned long);
	else
		return va_arg(*ap, unsigned int);
}

// Same as getuint but signed - can't use getuint
// because of sign extension
static long long
getint(va_list *ap, int lflag)
{
	if (lflag >= 2)
		return va_arg(*ap, long long);
	else if (lflag)
		return va_arg(*ap, long);
	else
		return va_arg(*ap, int);
}


// Main function to format and print a string.
void printfmt(void (*putch)(int, void*), void *putdat, const char *fmt, ...);

void
vprintfmt(void (*putch)(int, void*), void *putdat, const char *fmt, va_list ap)
{
	register const char *p;
	register int ch, err;
	unsigned long long num;
	int base, lflag, width, precision, altflag;
	char padc;

	while (1) {
		while ((ch = *(unsigned char *) fmt++) != '%') {
			if (ch == '\0')
				return;
			putch(ch, putdat);
		}

		// Process a %-escape sequence
		padc = ' ';
		width = -1;
		precision = -1;
		lflag = 0;
		altflag = 0;
	reswitch:
		switch (ch = *(unsigned char *) fmt++) {

		// flag to pad on the right
		case '-':
			padc = '-';
			goto reswitch;

		// flag to pad with 0's instead of spaces
		case '0':
			padc = '0';
			goto reswitch;

		// width field
		case '1':
		case '2':
		case '3':
		case '4':
		case '5':
		case '6':
		case '7':
		case '8':
		case '9':
			for (precision = 0; ; ++fmt) {
				precision = precision * 10 + ch - '0';
				ch = *fmt;
				if (ch < '0' || ch > '9')
					break;
			}
			goto process_precision;

		case '*':
			precision = va_arg(ap, int);
			goto process_precision;

		case '.':
			if (width < 0)
				width = 0;
			goto reswitch;

		case '#':
			altflag = 1;
			goto reswitch;

		process_precision:
			if (width < 0)
				width = precision, precision = -1;
			goto reswitch;

		// long flag (doubled for long long)
		case 'l':
			lflag++;
			goto reswitch;

		// character
		case 'c':
			putch(va_arg(ap, int), putdat);
			break;

		// error message
		case 'e':
			err = va_arg(ap, int);
			if (err < 0)
				err = -err;
			if (err >= MAXERROR || (p = error_string[err]) == NULL)
				printfmt(putch, putdat, "error %d", err);
			else
				printfmt(putch, putdat, "%s", p);
			break;

		// string
		case 's':
			if ((p = va_arg(ap, char *)) == NULL)
				p = "(null)";
			if (width > 0 && padc != '-')
				for (width -= strnlen(p, precision); width > 0; width--)
					putch(padc, putdat);
			for (; (ch = *p++) != '\0' && (precision < 0 || --precision >= 0); width--)
				if (altflag && (ch < ' ' || ch > '~'))
					putch('?', putdat);
				else
					putch(ch, putdat);
			for (; width > 0; width--)
				putch(' ', putdat);
			break;

		// (signed) decimal
		case 'd':
			num = getint(&ap, lflag);
			if ((long long) num < 0) {
				putch('-', putdat);
				num = -(long long) num;
			}
			base = 10;
			goto number;

		// unsigned decimal
		case 'u':
			num = getuint(&ap, lflag);
			base = 10;
			goto number;

		// (unsigned) octal
		case 'o':

			// Replace this with your code.
			//my code here start
			num = getuint(&ap,lflag);
			base = 8;
			//my code end
			putch('X', putdat);
			putch('X', putdat);
			putch('X', putdat);
			goto number;
			break;

		// pointer
		case 'p':
			putch('0', putdat);
			putch('x', putdat);
			num = (unsigned long long)
				(uintptr_t) va_arg(ap, void *);
			base = 16;
			goto number;

		// (unsigned) hexadecimal
		case 'x':
			num = getuint(&ap, lflag);
			base = 16;
		number:
			printnum(putch, putdat, num, base, width, padc);
			break;

		// escaped '%' character
		case '%':
			putch(ch, putdat);
			break;

		// unrecognized escape sequence - just print it literally
		default:
			putch('%', putdat);
			for (fmt--; fmt[-1] != '%'; fmt--)
				/* do nothing */;
			break;
		}
	}
}

void
printfmt(void (*putch)(int, void*), void *putdat, const char *fmt, ...)
{
	va_list ap;

	va_start(ap, fmt);
	vprintfmt(putch, putdat, fmt, ap);
	va_end(ap);
}

struct sprintbuf {
	char *buf;
	char *ebuf;
	int cnt;
};

static void
sprintputch(int ch, struct sprintbuf *b)
{
	b->cnt++;
	if (b->buf < b->ebuf)
		*b->buf++ = ch;
}

int
vsnprintf(char *buf, int n, const char *fmt, va_list ap)
{
	struct sprintbuf b = {buf, buf+n-1, 0};

	if (buf == NULL || n < 1)
		return -E_INVAL;

	// print the string to the buffer
	vprintfmt((void*)sprintputch, &b, fmt, ap);

	// null terminate the buffer
	*b.buf = '\0';

	return b.cnt;
}

int
snprintf(char *buf, int n, const char *fmt, ...)
{
	va_list ap;
	int rc;

	va_start(ap, fmt);
	rc = vsnprintf(buf, n, fmt, ap);
	va_end(ap);

	return rc;
}


```
####readline.c
```
#include <inc/stdio.h>
#include <inc/error.h>

#define BUFLEN 1024
static char buf[BUFLEN];

char *
readline(const char *prompt)
{
	int i, c, echoing;

	if (prompt != NULL)
		cprintf("%s", prompt);

	i = 0;
	echoing = iscons(0);
	while (1) {
		c = getchar();
		if (c < 0) {
			cprintf("read error: %e\n", c);
			return NULL;
		} else if ((c == '\b' || c == '\x7f') && i > 0) {
			if (echoing)
				cputchar('\b');
			i--;
		} else if (c >= ' ' && i < BUFLEN-1) {
			if (echoing)
				cputchar(c);
			buf[i++] = c;
		} else if (c == '\n' || c == '\r') {
			if (echoing)
				cputchar('\n');
			buf[i] = 0;
			return buf;
		}
	}
}

```
####string.c
```
// Basic string routines.  Not hardware optimized, but not shabby.

#include <inc/string.h>

// Using assembly for memset/memmove
// makes some difference on real hardware,
// but it makes an even bigger difference on bochs.
// Primespipe runs 3x faster this way.
#define ASM 1

int
strlen(const char *s)
{
	int n;

	for (n = 0; *s != '\0'; s++)
		n++;
	return n;
}

int
strnlen(const char *s, size_t size)
{
	int n;

	for (n = 0; size > 0 && *s != '\0'; s++, size--)
		n++;
	return n;
}

char *
strcpy(char *dst, const char *src)
{
	char *ret;

	ret = dst;
	while ((*dst++ = *src++) != '\0')
		/* do nothing */;
	return ret;
}

char *
strcat(char *dst, const char *src)
{
	int len = strlen(dst);
	strcpy(dst + len, src);
	return dst;
}

char *
strncpy(char *dst, const char *src, size_t size) {
	size_t i;
	char *ret;

	ret = dst;
	for (i = 0; i < size; i++) {
		*dst++ = *src;
		// If strlen(src) < size, null-pad 'dst' out to 'size' chars
		if (*src != '\0')
			src++;
	}
	return ret;
}

size_t
strlcpy(char *dst, const char *src, size_t size)
{
	char *dst_in;

	dst_in = dst;
	if (size > 0) {
		while (--size > 0 && *src != '\0')
			*dst++ = *src++;
		*dst = '\0';
	}
	return dst - dst_in;
}

int
strcmp(const char *p, const char *q)
{
	while (*p && *p == *q)
		p++, q++;
	return (int) ((unsigned char) *p - (unsigned char) *q);
}

int
strncmp(const char *p, const char *q, size_t n)
{
	while (n > 0 && *p && *p == *q)
		n--, p++, q++;
	if (n == 0)
		return 0;
	else
		return (int) ((unsigned char) *p - (unsigned char) *q);
}

// Return a pointer to the first occurrence of 'c' in 's',
// or a null pointer if the string has no 'c'.
char *
strchr(const char *s, char c)
{
	for (; *s; s++)
		if (*s == c)
			return (char *) s;
	return 0;
}

// Return a pointer to the first occurrence of 'c' in 's',
// or a pointer to the string-ending null character if the string has no 'c'.
char *
strfind(const char *s, char c)
{
	for (; *s; s++)
		if (*s == c)
			break;
	return (char *) s;
}

#if ASM
void *
memset(void *v, int c, size_t n)
{
	char *p;

	if (n == 0)
		return v;
	if ((int)v%4 == 0 && n%4 == 0) {
		c &= 0xFF;
		c = (c<<24)|(c<<16)|(c<<8)|c;
		asm volatile("cld; rep stosl\n"
			:: "D" (v), "a" (c), "c" (n/4)
			: "cc", "memory");
	} else
		asm volatile("cld; rep stosb\n"
			:: "D" (v), "a" (c), "c" (n)
			: "cc", "memory");
	return v;
}

void *
memmove(void *dst, const void *src, size_t n)
{
	const char *s;
	char *d;

	s = src;
	d = dst;
	if (s < d && s + n > d) {
		s += n;
		d += n;
		if ((int)s%4 == 0 && (int)d%4 == 0 && n%4 == 0)
			asm volatile("std; rep movsl\n"
				:: "D" (d-4), "S" (s-4), "c" (n/4) : "cc", "memory");
		else
			asm volatile("std; rep movsb\n"
				:: "D" (d-1), "S" (s-1), "c" (n) : "cc", "memory");
		// Some versions of GCC rely on DF being clear
		asm volatile("cld" ::: "cc");
	} else {
		if ((int)s%4 == 0 && (int)d%4 == 0 && n%4 == 0)
			asm volatile("cld; rep movsl\n"
				:: "D" (d), "S" (s), "c" (n/4) : "cc", "memory");
		else
			asm volatile("cld; rep movsb\n"
				:: "D" (d), "S" (s), "c" (n) : "cc", "memory");
	}
	return dst;
}

#else

void *
memset(void *v, int c, size_t n)
{
	char *p;
	int m;

	p = v;
	m = n;
	while (--m >= 0)
		*p++ = c;

	return v;
}

void *
memmove(void *dst, const void *src, size_t n)
{
	const char *s;
	char *d;

	s = src;
	d = dst;
	if (s < d && s + n > d) {
		s += n;
		d += n;
		while (n-- > 0)
			*--d = *--s;
	} else
		while (n-- > 0)
			*d++ = *s++;

	return dst;
}
#endif

void *
memcpy(void *dst, const void *src, size_t n)
{
	return memmove(dst, src, n);
}

int
memcmp(const void *v1, const void *v2, size_t n)
{
	const uint8_t *s1 = (const uint8_t *) v1;
	const uint8_t *s2 = (const uint8_t *) v2;

	while (n-- > 0) {
		if (*s1 != *s2)
			return (int) *s1 - (int) *s2;
		s1++, s2++;
	}

	return 0;
}

void *
memfind(const void *s, int c, size_t n)
{
	const void *ends = (const char *) s + n;
	for (; s < ends; s++)
		if (*(const unsigned char *) s == (unsigned char) c)
			break;
	return (void *) s;
}

long
strtol(const char *s, char **endptr, int base)
{
	int neg = 0;
	long val = 0;

	// gobble initial whitespace
	while (*s == ' ' || *s == '\t')
		s++;

	// plus/minus sign
	if (*s == '+')
		s++;
	else if (*s == '-')
		s++, neg = 1;

	// hex or octal base prefix
	if ((base == 0 || base == 16) && (s[0] == '0' && s[1] == 'x'))
		s += 2, base = 16;
	else if (base == 0 && s[0] == '0')
		s++, base = 8;
	else if (base == 0)
		base = 10;

	// digits
	while (1) {
		int dig;

		if (*s >= '0' && *s <= '9')
			dig = *s - '0';
		else if (*s >= 'a' && *s <= 'z')
			dig = *s - 'a' + 10;
		else if (*s >= 'A' && *s <= 'Z')
			dig = *s - 'A' + 10;
		else
			break;
		if (dig >= base)
			break;
		s++, val = (val * base) + dig;
		// we don't properly detect overflow!
	}

	if (endptr)
		*endptr = (char *) s;
	return (neg ? -val : val);
}

```






