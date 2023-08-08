---
sort: 2
---

#  EE Registers

## General-Purpose Registers (GPRs)

As per MIPS tradition, the EE has 32 GPRs. Notably, they are all 128-bit, though the full 128 bits are only used in certain instructions.

Name | Convention
---- | -------
zero | Hardwired to 0, writes are ignored
at | Temporary register used for pseudo-instructions
v0-v1 | Return register, holds values returned by functions
a0-a3 | Argument registers, holds first four parameters passed to a function
t0-t7 | Temporary registers. t0-t3 may also be used as additional argument registers
s0-s7 | Saved registers. Functions must save and restore these before using them
t8-t9 | Temporary registers
k0-k1 | Reserved for use by kernels
gp | Global pointer
sp | Stack pointer
fp | Frame pointer
ra | Return address. Used by JAL and (usually) JALR to store the address to return to after a function

> Aside from zero, all GPRs may be freely accessed if convention rules are respected.

## Special Registers

Name | Purpose
---- | -------
pc | Program counter, address of currently-executing instruction (32-bit)
hi/lo | Stores multiplication and division results (64-bit)
hi1/lo1 | Used by MULT1/DIV1 type instructions, same as above (64-bit)
sa | Shift amount used by QFSRV instruction

