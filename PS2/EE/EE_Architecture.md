---
sort: 1
---
# The EE Architecture

On real hardware, the EE itself contains many different hardware units along with the main CPU, such as the DMAC and IPU. This document will consider the EE as only the MIPS CPU and its coprocessors.
- Mix of MIPS III and MIPS IV with dozens of custom instructions
- Speed: 294.912 MHz
- Superscalar, able to issue two instructions per cycle in ideal conditions
- Support for custom integer SIMD instructions known as MMI (MultiMedia Instructions)
- 16 KB instruction cache
- 8 KB data cache
- 16 KB scratchpad. Fast memory directly accessible by program and DMAC
- COP0 system coprocessor. Responsible for handling memory management, exceptions, caches, and performance counters
- COP1 FPU. Fast single-precision unit
- COP2 VU0. SIMD floating-point processor that can either run as a separate CPU or be used by the EE core for COP2 instructions
  
See Vector Unit (VU) for a general discussion on the VUs, including VU0.
