---
sort: 4
---

# GS Frame and Z Buffers

## `4Ch/4Dh` FRAME_1/2
- 0-8    Base pointer in words/2048
- 16-21  Buffer width in pixels/64
- 24-29  Format
  + 00h=PSMCT32
  + 01h=PSMCT24
  + 02h=PSMCT16
  + 0Ah=PSMCT16S
  + 30h=PSMZ32
  + 31h=PSMZ24
  + 32h=PSMZ16
  + 3Ah=PSMZ16S
- 32-63  Framebuffer mask

Bits 32-63 prevent the specified bits in the framebuffer from being updated by the following formula:
```  
final_color = (final_color & ~mask) | (frame_color & mask)
```

This mask is applied before format conversions, e.g., bit 63 of FRAME will affect bit 15 (alpha bit) of a 16-bit color.

## `4Eh/4Fh` ZBUF_1/2
- 0-8    Base pointer in words/2048
- 24-27  Format
  + 00h=PSMZ32
  + 01h=PSMZ24
  + 02h=PSMZ16
  + 0Ah=PSMZ16S
- 32     Buffer mask (1=do not update zbuffer)
  
The zbuffer's width is the same as the framebuffer's, specified in FRAME.
