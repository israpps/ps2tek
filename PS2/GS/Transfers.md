---
sort: 5
---

# GS Transfers

The GS supports GIF->VRAM, VRAM->VRAM, and VRAM->GIF data transfers.

## `50h` BITBLTBUF
- 0-13    Source base pointer in words/64
- 16-21   Source buffer width in pixels/64
- 24-29   Source format
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
- 32-45   Destination base pointer in words/64
- 48-53   Destination buffer width in pixels/64
- 56-61   Destination format (same as source format)

In VRAM->VRAM transfers, the source and destination formats must have the same bits per pixel.

## `51h` TRXPOS
- 0-10    X for source rectangle
- 16-26   Y for source rectangle
- 32-42   X for destination rectangle
- 48-58   Y for destination rectangle
- 59-60   Transmission order for VRAM->VRAM transfers
  + 0=Upper-left->lower-right
  + 1=Lower-left->upper-right
  + 2=Upper-right->lower-left
  + 3=Lower-right->upper-left

X and Y are in units of pixels and define the upper-left corner of their respective rectangle.

```note
During transfer, X and Y wrap around if they exceed 2048, e.g., by the following formula:

<code>X = (TRXPOS.X + TRXREG.width) % 2048</code>

```


## `52h` TRXREG
- 0-11    Width in pixels of transmission area
- 32-43   Height in pixels of transmission area

53h TRXDIR
- 0-1     Transmission direction
  + 0=GIF->VRAM
  + 1=VRAM->GIF
  + 2=VRAM->VRAM
  + 3=Deactivated
```note
that the privileged register BUSDIR must be set appropriately for GIF->VRAM and VRAM->GIF.
```

## `54h` HWREG
- 0-63    Data to be transferred for GIF->VRAM

The `IMAGE` GIFtag format is a shortcut for writing to this register. Data is packed according to the format. For example, PSMCT4 will have 16 4-bit pixels per doubleword.
