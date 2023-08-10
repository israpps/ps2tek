---
sort: 8
---

# GS Alpha Blending

## `42h`/`43h` ALPHA_1/2
- 0-1   Spec A
- 2-3   Spec B
- 4-5   Spec C
- 6-7   Spec D
- 8-15  Alpha FIX

The GS's alpha blending formula is fixed but it contains four variables that can be reconfigured:
```
Output = (((A - B) * C) >> 7) + D
```
A, B, and D are colors and C is an alpha value. Their specific values come from the ALPHA register:

| - | A | B | C | D | 
| :---: | :---: | :---: | :---: | :---: |
| 0 | Source RGB | Source RGB | Source alpha | Source RGB | 
| 1 | Framebuffer RGB | Framebuffer RGB | Framebuffer alpha | Framebuffer RGB | 
| 2 | 0 | 0 | FIX | 0 | 
| 3 | Reserved | Reserved | Reserved | Reserved |

Internally, alpha-blending treats each color component as 9-bit. The output is then clamped accordingly by COLCLAMP.

## `46h` COLCLAMP
- 0    8-bit signed clamp (0=8-bit AND)

When COLCLAMP is 1, RGB components will be 0 if negative after alpha-blending or `0xFF` if `0x100` or above. Otherwise, each color component will be ANDed with `0xFF`.
