---
sort: 10
---


# EE COP1 Registers

## Floating-point Registers (FPRs)
Name | Convention
---- | -------
f0-f3 | Return values
f4-f11 | Temporary registers
f12-f19 | Argument registers
f20-f31 | Saved registers

## Special Registers (accessed through CFC1/CTC1)
Name | Purpose
---- | -------
fcr0 | Reports implementation and revision of FPU
fcr31 | Control register, stores status flags
