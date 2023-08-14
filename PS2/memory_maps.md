---
sort: 1
---

# Memory Map

## EE Virtual/Physical Memory Map
```
  KUSEG: 00000000h-7FFFFFFFh User segment
  KSEG0: 80000000h-9FFFFFFFh Kernel segment 0
  KSEG1: A0000000h-BFFFFFFFh Kernel segment 1
  KSSEG: C0000000h-DFFFFFFFh Supervisor segment
  KSEG3: E0000000h-FFFFFFFFh Kernel segment 3
  
  Virtual    Physical
  00000000h  00000000h  32 MB    Main RAM (first 1 MB reserved for kernel)
  20000000h  00000000h  32 MB    Main RAM, uncached
  30100000h  00100000h  31 MB    Main RAM, uncached and accelerated
  10000000h  10000000h  64 KB    I/O registers
  11000000h  11000000h  4 KB     VU0 code memory
  11004000h  11004000h  4 KB     VU0 data memory
  11008000h  11008000h  16 KB    VU1 code memory
  1100C000h  1100C000h  16 KB    VU1 data memory
  12000000h  12000000h  8 KB     GS privileged registers
  1C000000h  1C000000h  2 MB     IOP RAM
  1FC00000h  1FC00000h  4 MB     BIOS, uncached (rom0)
  9FC00000h  1FC00000h  4 MB     BIOS, cached (rom09)
  BFC00000h  1FC00000h  4 MB     BIOS, uncached (rom0b)
  70000000h  ---------  16 KB    Scratchpad RAM (only accessible via virtual addressing)
```
> EE RAM is reportedly expandable up to 256 MB. However, the maximum seen in practice is 128 MB, for special TOOL consoles.

## IOP Physical Memory Map
```
  KUSEG: 00000000h-7FFFFFFFh User segment
  KSEG0: 80000000h-9FFFFFFFh Kernel segment 0
  KSEG1: A0000000h-BFFFFFFFh Kernel segment 1
  
  Physical
  00000000h  2 MB     Main RAM (same as on PS1)
  1D000000h           SIF registers
  1F800000h  64 KB    Various I/O registers
  1F900000h  1 KB     SPU2 registers
  1FC00000h  4 MB     BIOS (rom0) - Same as EE BIOS
  
  FFFE0000h (KSEG2)   Cache control
```
## Additional Memory
- 4 MB   GS VRAM (used for framebuffer, textures, zbuffer, etc)
- 2 MB   SPU2 work RAM - quadrupled from PS1's SPU
- 8 MB   Memory card
