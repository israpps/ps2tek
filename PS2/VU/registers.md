---
sort: 2
---

# VU Registers

## General-purpose registers

Each VU contains 32 vector floating-point registers (vf00-vf31) and 16 16-bit integer registers (vi00-vi15).

A vector register has 4 32-bit single-precision floating-point elements: `{x, y, z, w}`

vi00 is hardwired to 0. vf00 is hardwired to the vector {0.0, 0.0, 0.0, 1.0}. That is to say, vf00.w = 1.0.

### Accumulator (ACC)
The accumulator is a special register with the same format as the 32 general-purpose vector registers.
It is intended to be used as an intermediate result by certain instructions.

### Q and P
Q and P are 32-bit floating point registers. Operations that use the FDIV or EFU units store their result in the Q and P registers respectively.
For example, a DIV stores its result in Q, and ESQRT stores its result in P.

### MAC Flags
Each FMAC unit (corresponding to a vector field) has four MAC flags, which can be read by various instructions.
The MAC flags are overflow, underflow, sign, and zero. The full 16-bit MAC flag register has this format:

```
15                                              0
-------------------------------------------------
|Ox|Oy|Oz|Ow|Ux|Uy|Uz|Uw|Sx|Sy|Sz|Sw|Zx|Zy|Zz|Zw|
-------------------------------------------------
```

Zero is set when the result is 0.0f or -0.0f (which also clears overflow and underflow), and sign is set when the result is negative (bit 31 is set).

Overflow and underflow are set when the exponent field is 0xFF or 0x0 respectively.

```warning
MAC flags are modified in the "writeback" stage in the FMAC pipeline. However, an instruction like FMAND will read the flags in the register read stage, and this does not constitute a hazard.
This means every modification to the MAC flags has a delay of four cycles (not necessarily four instructions).
```

### Clip Flags
The clipping flags register is 24-bit and contains the result of up to four CLIP instructions. Every CLIP instruction produces six flags: -x, +x, -y, +y, -z, +z. They are stored in the following format:
```
5                 0
-------------------
|-z|+z|-y|+y|-x|+x|
-------------------
```

Like the MAC flags, the clip flags are also pipelined (and thus delayed).

Status Flags
```
11                                  0
-------------------------------------
|DS|IS|OS|US|SS|ZS|D |I |O |U |S |Z |
-------------------------------------
```


The Z, S, U, and O flags are set when any of the field flags for zero, sign, underflow, and overflow are set in the MAC register, respectively.

The I flag is an invalid flag, set to 1 when 0/0 is executed by DIV or when a negative number is used for SQRT/RSQRT.

The D flag is set for division by zero (except for 0/0) on DIV/RSQRT and is always cleared on SQRT.

Bits 6-11 are sticky flags - they do not represent the current state but rather the ORed result of all previous states.

If Z goes from 1 to 0 for instance, ZS will always remain 1 until the status register is written to.
