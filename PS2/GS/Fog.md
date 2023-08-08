---
sort: 7
---

# GS Fog

## `3Dh` FOGCOL
- 0-7   R
- 8-15  G
- 16-23 B
  
`FOGCOL` represents the color of a "distant" object, or one enshrounded in fog. See below for details.

## `0Ah` FOG
- 0-7  Fog effect
  
FOG gives a fog effect F to the current vertex. XYZ2F/XYZ3F also modify this register.

During rasterization, if fog is enabled in PRIM/PRMODE, F is linearly interpolated for all vertices. The fog equation is then applied to an outputted texture color after the color function has been applied:

```
Output = ((F * input) >> 8) + (((255 - F) * FOGCOL) >> 8)
```

So a value of 0xFF results in no change and a value of 0 completely converts the color to FOGCOL.

You can see that fog equation is just another alpha blending equation.
