---
sort: 6
---

# GS Textures

## `06h/07h` TEX0_1/2
- 0-13    Base pointer in words/64
- 14-19   Buffer width in pixels/64
- 20-25   Texture format
  + 00h=PSMCT32
  + 01h=PSMCT24
  + 02h=PSMCT16
  + 0Ah=PSMCT16S
  + 13h=PSMCT8
  + 14h=PSMCT4
  + 1Bh=PSMCT8H
  + 24h=PSMCT4HL
  + 2Ch=PSMCT4HH
  + 30h=PSMZ32
  + 31h=PSMZ24
  + 32h=PSMZ16
  + 3Ah=PSMZ16S
- 26-29   Texture width (width = min(2^value, 1024))
- 30-33   Texture height (height = min(2^value, 1024))
- 34      Alpha control (0=texture is RGB, 1=texture is RGBA)
- 35-36   Color function
  + 0=Modulate
  + 1=Decal
  + 2=Highlight
  + 3=Highlight2
- 37-50   CLUT base pointer in words/64
- 51-54   CLUT format
  + 00h=PSMCT32
  + 02h=PSMCT16
  + 0Ah=PSMCT16S
- 55      CLUT uses CSM2 (0=CSM1)
- 56-60   CLUT entry offset/16 (In CSM2, this value must be 0)
- 61-63   CLUT cache control
  + 0=Do not reload cache
  + 1=Reload cache
  + 2=Reload cache and copy CLUT base pointer to CBP0
  + 3=Reload cache and copy CLUT base pointer to CBP1
  + 4=IF CLUT base pointer != CBP0, reload cache and copy pointer to CBP0
  + 5=IF CLUT base pointer != CBP1, reload cache and copy pointer to CBP1

## `14h/15h` TEX1_1/2
- 0       LOD (level of detail) calculation method
  + 0=LOD=`(log2(1/abs(Q))<= 0` (reduced texture)
  + 0=nearest
  + 1=bilinear
  + 2=nearest_mipmap_nearest
  + 3=nearest_mipmap_bilinear
  + 4=bilinear_mipmap_nearest
  + 5=bilinear_mipmap_bilinear
- 9       Automatic calculation of mipmap levels 1-3 (0=use MIPTBP1)
- 19-20   L parameter
- 32-43   K parameter (signed fixed-point, 7 bits whole, 4 bits fractional)

16h/17h TEX2_1/2
- 20-25   Texture format (same as TEX0)
- 37-50   CLUT base pointer in words/64
- 51-54   CLUT format
- 55      CLUT uses CSM2 (0=CSM1)
- 56-60   CLUT entry offset/16
- 61-63   CLUT cache control (same as TEX0)
TEX2 is a subset of TEX0. This is useful for modifying texture format and CLUT information when the texture base pointer, width, etc. must stay the same.

## `3Fh` TEXFLUSH
Writing any value to this register will invalidate the texture cache. Do this in the following situations:
- Using newly transferred texture data
- Using newly transferred CLUT data or reloading the CLUT cache
- Using framebuffer or zbuffer data as textures

## Texture coordinates
The GS supports STQ texture coordinates and UV texel coordinates. The relationship between the two is as follows.

`U=(S/Q)*TEX0.texwidth`
`V=(T/Q)*TEX0.texheight`

Q is defined by RGBAQ and used for perspective correction. It is not possible to use perspective correction when directly using UV coordinates.

## `02h` ST
- 0-31    S (lower 8 bits are rounded down to zero)
- 32-63   T (lower 8 bits are rounded down to zero)

S and T are (mostly) IEEE 754-compliant single-precision floating-point values. For both S and T, the range [0.0, 1.0] refers to the whole texture.

## `03h` UV
- 0-13    U
- 16-29   V

U and V are unsigned 10-bit fixed-point integers with a 4-bit fractional component. The ranges [0, TEXWIDTH] and [0, TEXHEIGHT] for U and V respectively refer to the whole texture.

Color function TEX0 defines four possible color functions that can be used to blend texture and vertex colors:
```
               RGB                            RGBA
  Modulate     Rv = (Rv * Rt) >> 7            <- Same
               Gv = (Gv * Gt) >> 7            <- Same
               Bv = (Bv * Bt) >> 7            <- Same
               Av = Av                        Av = (Av * At) >> 7
  Decal        Rv = Rt                        <- Same
               Gv = Gt                        <- Same
               Bv = Bt                        <- Same
               Av = Av                        Av = At
  Highlight    Rv = ((Rv * Rt) >> 7) + Av     <- Same
               Gv = ((Gv * Gt) >> 7) + Av     <- Same
               Bv = ((Bv * Bt) >> 7) + Av     <- Same
               Av = Av                        Av = At + Av
  Highlight2   Same as Highlight              Same as Highlight, but Av = At
```
