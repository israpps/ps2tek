---
sort: 9
---

# GS Tests and Pixel Control

## `40h`/`41h` SCISSOR_1/2
- 0-10   X0
- 16-26  X1
- 32-42  Y0
- 48-58  Y1

SCISSOR defines a rectangle with 11-bit unsigned integer coordinates, which range from `0` to `2047`. During drawing, pixels that fall outside the boundaries of this rectangle fail automatically and are not processed.

## `47h`/`48h` TEST_1/2
- 0      Alpha test enabled
- 1-3    Alpha test method
  + 0=NEVER (all pixels fail)
  + 1=ALWAYS (all pixels pass)
  + 2=LESS (pixel alpha < AREF passes)
  + 3=LEQUAL (pixel alpha <= AREF passes)
  + 4=EQUAL (pixel alpha == AREF passes)
  + 5=GEQUAL (pixel alpha >= AREF passes)
  + 6=GREATER (pixel alpha > AREF passes)
  + 7=NEQUAL (pixel alpha != AREF passes)
- 4-11   AREF
- 12-13  Alpha test failure processing
  + 0=Neither framebuffer nor zbuffer are updated.
  + 1=Only framebuffer is updated.
  + 2=Only zbuffer is updated.
  + 3=Only RGB in framebuffer is updated.
- 14     Destination alpha test enabled
- 15     Destination alpha test method
  + 0=destination alpha bit == 0 passes
  + 1=destination alpha bit == 1 passes
- 16     Depth test enabled (0 is prohibited?)
- 17-18  Depth test method
  + 0=NEVER (all pixels fail)
  + 1=ALWAYS (all pixels pass)
  + 2=GEQUAL (pixel Z >= zbuffer Z passes)
  + 3=GREATER (pixel Z > zbuffer Z passes)

```note
on destination alpha test:

The alpha bit tested depends on the framebuffer format. If the format is PSMCT32, bit 7 of alpha is tested. If the format is PSMCT16, the sole alpha bit is tested. If the format is PSMCT24, all pixels pass due to the lack of alpha.
```
