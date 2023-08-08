---
sort: 1
---

# GS Register List

## Internal registers (accessible via GIF)


```
  00h     PRIM
  01h     RGBAQ
  02h     ST
  03h     UV
  04h     XYZF2
  05h     XYZ2
  06h/07h TEX0_1/2
  08h/09h CLAMP_1/2
  0Ah     FOG
  0Ch     XYZF3
  0Dh     XYZ3
  14h/15h TEX1_1/2
  16h/17h TEX2_1/2
  18h/19h XYOFFSET_1/2
  1Ah     PRMODECONT
  1Bh     PRMODE
  1Ch     TEXCLUT
  22h     SCANMSK
  34h/35h MIPTBP1_1/2
  36h/37h MIPTBP2_1/2
  3Bh     TEXA
  3Dh     FOGCOL
  3Fh     TEXFLUSH
  40h/41h SCISSOR_1/2
  42h/43h ALPHA_1/2
  44h     DIMX
  45h     DTHE
  46h     COLCLAMP
  47h/48h TEST_1/2
  49h     PABE
  4Ah/4Bh FBA_1/2
  4Ch/4Dh FRAME_1/2
  4Eh/4Fh ZBUF_1/2
  50h     BITBLTBUF
  51h     TRXPOS
  52h     TRXREG
  53h     TRXDIR
  54h     HWREG
  60h     SIGNAL
  61h     FINISH
  62h     LABEL
```

## Privileged registers (accessible via EE)

```
  12000000h    PMODE
  12000020h    SMODE2
  12000070h    DISPFB1
  12000080h    DISPLAY1
  12000090h    DISPFB2
  120000A0h    DISPLAY2
  120000B0h    EXTBUF
  120000C0h    EXTDATA
  120000D0h    EXTWRITE
  120000E0h    BGCOLOR
  12001000h    CSR
  12001010h    IMR
  12001040h    BUSDIR
  12001080h    SIGLBLID
```
