---
sort: 2
---

# GS Primitives

The GS supports point, line, triangle, and sprite (2D rectangle with two points) primitives.

## `00h` PRIM
- 0-2   Primitive type
  + 0=Point
  + 1=Line
  + 2=LineStrip
  + 3=Triangle
  + 4=TriangleStrip
  + 5=TriangleFan
  + 6=Sprite
  + 7=Reserved
- 3     Gourand shading
- 4     Texture mapping
- 5     Fog
- 6     Alpha blending
- 7     Antialiasing
- 8     Use UV for texture coordinates (0=Use STQ)
- 9     Use Context 2's registers for drawing (0=Use Context 1)
- 10    Fix fragment value (related to antialiasing?)

## `1Ah` PRMODECONT

When this register is set to 1, bits 3-10 in PRIM are used when rendering primitives. Otherwise, bits 3-10 in PRMODE are used.

## `1Bh` PRMODE

Same as PRIM, except bits 0-2 are unused.

PRMODE can be useful when you want to draw multiple primitives with different attributes but the same type.
