---
sort: 1
---

# VU Architecture


Custom SIMD floating-point processors designed by Sony

Speed: 294.912 MHz (same as EE)

Executes two instructions per cycle through upper and lower pipelines - each pipeline is specialized and can only execute certain instructions

4/16 KB of instruction ("micro") memory for VU0 and VU1 respectively

4/16 KB of data memory for VU0 and VU1 respectively

32 128-bit vector registers, 16 16-bit integer registers, and an assortment of special registers

FDIV unit: Used for division and square root operations

Elementary Function Unit (EFU): Exclusive to VU1. Used for complex calculations such as square of sums, sine, and e^x

MAC/CLIP flags: Processor flags used to compare the results of floating-point arithmetic

XGKICK: Exclusive to VU1. Transfers data directly to the GIF through PATH1

Similar decoding and quirks to standard MIPS, such as branch delay slots
