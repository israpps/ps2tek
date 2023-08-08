---
sort: 1
---

# GIF I/O

## `10003000h` GIF_CTRL - Control register (W)
- 0     Reset GIF
- 1-2   Unused
- 3     Temporary stop (1=stop transfers, 0=restart transfers)
- 4-31  Unused

## `10003010h` GIF_MODE - Mode of operation (W)
- 0     Mask PATH3 (1=Mask)
- 1     Unused
- 2     Intermittent mode
  
When PATH3 is masked by this register and GIF DMA is ongoing, the mask applies once GIF DMA ends its transfer.

## `10003020h` GIF_STAT - Status register (R)
- 0     PATH3 masked by GIF_MODE
- 1     PATH3 masked by VIF1 MASKP3 register
- 2     Intermittent mode activated
- 3     Temporary stop 
- 4     Unused
- 5     PATH3 interrupted (by intermittent mode?)
- 6     PATH3 queued
- 7     PATH2 queued
- 8     PATH1 queued
- 9     Output path (1=transfer ongoing)
- 10-11 Active path
  + 0=Idle
  + 1=PATH1
  + 2=PATH2
  + 3=PATH3
- 12    Transfer direction (0=EE->GS, 1=GS->EE)
- 13-23 Unused
- 24-28 Data in GIF FIFO (in quadwords, max 16)
- 29-31 Unused

## `10003040h` GIF_TAG0 (R)
- 0-31  Bits 0-31 of most recently read GIFtag
## `10003050h` GIF_TAG1 (R)
- 0-31  Bits 32-63 of most recently read GIFtag
## `10003060h` GIF_TAG2 (R)
- 0-31  Bits 64-95 of most recently read GIFtag
## `10003070h` GIF_TAG3 (R)
- 0-31  Bits 96-127 of most recently read GIFtag
These registers are only readable when the GIF has been paused by GIF_CTRL.
For details on the GIFtag format, see [GIFtags](./GIFtags.md)

## `10003080h` GIF_CNT (R)
- 0-14  Backwards loop counter from NLOOP
  + Decrements to zero
- 15    Unused
- 16-19 Register descriptor in progress
  + 0=highest
  + 1=lowest
  + 2=2nd lowest
  + ...
  + 15=15th lowest
- 20-29 VU data address being transferred
- 30-31 Unused

Only accessible when GIF is paused by GIF_CTRL.

## `10003090h` GIF_P3CNT (R)
- 0-14  Backwards loop counter from PATH3 NLOOP when PATH3 is interrupted
- 15-31 Unused
  
Only accessible when GIF is paused by GIF_CTRL.

## `100030A0h` GIF_P3TAG (R)
- 0-31  Bits 0-31 of PATH3 GIFtag when PATH3 is interrupted
  
Only accessible when GIF is paused by GIF_CTRL.
