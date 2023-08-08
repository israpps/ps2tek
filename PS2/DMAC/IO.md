---
sort: 1
---

# DMAC I/O

## Channels
- `100080xxh`: VIF0
- `100090xxh`: VIF1 (can act as PATH2 for GIF)
- `1000A0xxh`: GIF (PATH3)
- `1000B0xxh`: IPU_FROM
- `1000B4xxh`: IPU_TO
- `1000C0xxh`: SIF0 (from IOP)
- `1000C4xxh`: SIF1 (to IOP)
- `1000C8xxh`: SIF2 (bidirectional, used for PSX mode and debugging)
- `1000D0xxh`: SPR_FROM
- `1000D4xxh`: SPR_TO

## `1000xx00h` Dn_CHCR - Channel control (R/W)
- 0     DIR - Direction (0=to memory, 1=from memory) 
        Only effective for VIF1 and SIF2
- 1     Unused
- 2-3   MOD - Mode (0=normal, 1=chain, 2=interleave)
- 4-5   ASP - Address stack pointer
- 6     TTE - Transfer DMAtag (only in source chain mode)
- 7     TIE - Enable IRQ bit in DMAtag
- 8     STR - Start/busy
- 9-15  Unused
- 16-31 TAG - Bits 16-31 of most recently read DMAtag

## `1000xx10h` Dn_MADR - Channel address (R/W)

## `1000xx30h` Dn_TADR - Channel tag address (R/W)

- 0-30  Address (lower 4 bits must be zero)
- 31    Memory selection (0=RAM, 1=scratchpad)

The channel will read from/write to MADR directly during a transfer. TADR is only used for chain mode.

MADR and TADR update while a transfer is ongoing, i.e., the DMAC has no internal channels for memory addresses.

Certain games expect MADR and TADR to increment during a transfer.

## `1000xx20h` Dn_QWC - Quadword count (R/W)
- 0-15  Quadwords
- 16-31 Unused

In normal and interleaved mode, the transfer ends when QWC reaches zero. Chain mode behaves differently
see [DMAC Chain Mode](./Chain_mode.md)

## `1000xx40h` Dn_ASR0 - Channel saved tag address (R/W)
## `1000xx50h` Dn_ASR1 - Channel saved tag address (R/W)
- 0-30  Tag address (lower 4 bits must be zero)
- 31    Memory selection (0=RAM, 1=scratchpad)

ASR0/ASR1 can only be used by the VIF0, VIF1, and GIF channels.

## `1000xx80h` Dn_SADR - Channel scratchpad address (R/W)
- 0-13  Address (lower 4 bits must be zero)
- 14-31 Unused

SADR is only used by SPR_FROM and SPR_TO.

## `1000E000h` D_CTRL - DMAC control (R/W)
- 0    DMA enable
- 1    Cycle stealing on
- 2-3  MFIFO drain channel
  + 0=None
  + 1=Reserved
  + 2=VIF1
  + 3=GIF
- 4-5  Stall control channel
  + 0=None
  + 1=SIF0
  + 2=SPR_FROM
  + 3=IPU_FROM
- 6-7  Stall control drain channel
  + 0=None
  + 1=VIF1
  + 2=GIF
  + 3=SIF1
- 8-10 Release cycle period
  + 0=8
  + 1=16
  + 2=32
  + 3=64
  + 4=128
  + 5=256
When cycle stealing is on, the release cycle period controls how long the EE has control of the bus.

## `1000E010h` D_STAT - DMAC interrupt status (R/W)
- 0-9    Channel interrupt status (1=IRQ, write 1 to clear)
- 10-12  Unused
- 13     DMA stall interrupt status
- 14     MFIFO empty interrupt status
- 15     BUSERR interrupt status
- 16-25  Channel interrupt mask (1=enabled, write 1 to reverse)
- 26-28  Unused
- 29     Stall interrupt mask
- 30     MFIFO empty mask

INT1 is asserted when (status & mask) != 0.

## `1000E020h`` D_PCR - DMAC priority control (R/W)
- 0-9    COP0 condition control
- 10-15  Unused
- 16-25  Channel priority (0=Channel disabled, only if D_PCR.31 is true)
- 26-30  Unused
- 31     Priority enable
COP0 control determines whether or not CPCOND0 is activated. If `(~control | D_STAT.0-9) == 0x3FF`, CPCOND0 is set. This is useful for knowing when multiple DMA transfers have finished without using interrupts.

## `1000E030h` D_SQWC - DMAC skip quadword (R/W)
- 0-7   Quadwords to skip
- 8-15  Unused
- 16-23 Quadwords to transfer
- 24-31 Unused

In interleave mode, every time D_SQWC.16-23 quadwords have been transferred, D_SQWC.0-7 quadwords are skipped.

## `1000E040h` D_RBSR - DMAC ringbuffer size (R/W)
  4-30  Size of MFIFO buffer in quadwords, minus 1

## `1000E050h` D_RBOR - DMAC ringbuffer offset (R/W)
  0-30  Starting address of MFIFO buffer. Lower 4 bits are 0

## `1000F520h` D_ENABLER - DMAC disabled status (R)
## `1000F590h` D_ENABLEW - DMAC disable (W)
- 16    DMAC disabled

  All other bits appear to be garbage, but writable
  SCPH-39001 (and other BIOSes?) seems to expect D_ENABLER to be set to `1201h`` upon boot
