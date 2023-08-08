---
sort: 3
---

# GIF Data Formats

The data format after a GIFtag varies depending on the value of GIFtag.58-59. The GIF can transfer to the GS in PACKED, REGLIST, or IMAGE formats.

## PACKED Format
Data is transferred in units of quadwords (16 bytes). Total amount of data in the GIF primitive = NLOOP * NREGS.

All registers not specified below output the lower 64-bits of the quadword to their GS register directly. The upper 64-bits are discarded.

### Current reg=0h PRIM
- 0-10    Data to write to GS PRIM
- 11-127  Unused

### Current reg=1h RGBA
Writes to RGBAQ register (Q is unchanged)
- 0-7     R
- 8-31    Unused
- 32-39   G
- 40-63   Unused
- 64-71   B
- 72-95   Unused
- 96-103  A
- 104-127 Unused
  
R, G, B, and A are 8-bit values. Q is set by the STQ command.

### Current reg=2h STQ
Writes to ST register and Q component of RGBAQ.
- 0-31    S
- 32-63   T
- 64-95   Q
- 96-127  Unused
- 
S, T, and Q are single-precision (32-bit) floats in IEEE 754 format.

### Current reg=3h UV
- 0-13    U
- 14-31   Unused
- 32-45   V
- 46-127  Unused
  
U and V are 14-bit unsigned fixed-point integers with a 4-bit fractional component.

### Current reg=4h XYZ2F/XYZ3F
- 0-15    X
- 16-31   Unused
- 32-47   Y
- 48-67   Unused
- 68-91   Z
- 92-99   Unused
- 100-107 F
- 108-110 Unused
- 111     Disable drawing (1=write to XYZ3F, 0=write to XYZ2F)
- 112-127 Unused
  
X and Y are signed 16-bit fixed-point integers with a 4-bit fractional component.

Z is a 24-bit integer, and F is an 8-bit integer.

The "disable drawing" bit controls whether the data is written to XYZ2F or XYZ3F.

### Current reg=5h XYZ2/XYZ3
- 0-15    X
- 16-31   Unused
- 32-47   Y
- 48-63   Unused
- 64-95   Z
- 96-110  Unused
- 111     Disable drawing (1=write to XYZ3F, 0=write to XYZ2F)
- 112-127 Unused
Similar to XYZ2F/XYZ3F above, except there is no F register and Z is 32-bit.

### Current reg=Ah FOG
- 0-99    Unused
- 100-107 F
- 108-127 Unused

### Current reg=Eh A+D (output data to specified address)
- 0-63    Data
- 64-71   Register address
- 72-127  Unused
Outputs data to the given register address.

```warning
Do not use this register descriptor on PATH3 when PATH1 is also running, or the GS may lock up!
```

### Current reg=Fh NOP
Data not output to GS.

## REGLIST Format
Total data in GIF primitive = NREGS * NLOOP, in units of doublewords. (64-bits)

In REGLIST, a doubleword is sent directly to the register descriptor. Every quadword contains data for two registers. When the data is prepared properly, this can multiply data density by up to a factor of 2.

When NREGS * NLOOP is odd, the last doubleword in a primitive is discarded.

## IMAGE Format
Total data in GIF primitive = NLOOP only, in units of quadwords.

IMAGE is a shortcut for writing to the GS HWREG register, which transfers textures and other data to VRAM. Each quadword has enough data for two writes to HWREG.
