---
sort: 2
---

# IOP DMA

The IOP re-uses the same DMA channels found in the PS1, and it contains additional channels for new peripherals.

## Channels
- Old channels
  + `1F80108xh`  0   MDECin
  + `1F80109xh`  1   MDECout
  + `1F8010Axh`  2   SIF2 (EE<->IOP, GPU in PS1 mode)
  + `1F8010Bxh`  3   CDVD (CDROM in PS1 mode)
  + `1F8010Cxh`  4   SPU1
  + `1F8010Dxh`  5   PIO
  + `1F8010Exh`  6   OTC
  
- New channels
  + `1F80150xh`  `7`   SPU2
  + `1F80151xh`  `8`   DEV9 (expansion port)
  + `1F80152xh`  `9`   SIF0 (IOP->EE, uses TADR)
  + `1F80153xh`  `10`  SIF1 (EE->IOP)
  + `1F80154xh`  `11`  SIO2in
  + `1F80155xh`  `12`  SIO2out

### `1F801xx0h+N*10h` Dn_MADR
- 0-23  Start address of transfer in RAM
- 24-31 Unused, zero

### `1F801xx4h+N*10h` Dn_BCR
- 0-15  Block size in words (0=0x10000)
- 16-31 Block count

### `1F801xx8h+N*10h` Dn_CHCR
- 0     Transfer direction (0=to RAM, 1=from RAM)
- 1     MADR increment per step (0=+4, 1=-4)
- 2-7   Unused
- 8     When 1:
  + Burst mode: enable "chopping" (cycle stealing by CPU)
  + Slice mode: Causes DMA to hang
  + Linked-list/Chain mode: Transfer header/tag before data
- 9-10  Transfer mode
  + 0=Burst (transfer data all at once, only low 16 bits of BCR are valid)
  + 1=Slice (transfer in units of BCR blocks and arbitrate)
  + 2=Linked-list mode
  + 3=Chain mode (uses TADR)
- 11    Unknown, used by iLink DMA
- 12-15 Unused
- 16-18 Chopping DMA window size (1 << N words)
- 19    Unused
- 20-22 Chopping CPU window size (1 << N cycles)
- 23    Unused
- 24    Start transfer (0=stopped/completed, 1=start/busy)
- 28    Force transfer start without waiting for DREQ
- 29    In forced-burst mode, pauses transfer while set.
  + In other modes, stops bit 28 from being cleared after a slice is transferred. No effect when transfer was caused by a DREQ.
- 30    Perform bus snooping (allows DMA to read from IOP cache?)
- 31    iLink automatic response - unknown purpose

### `1F801xxCh+N*10h` Dn_TADR
- 0-23  Start address of list of tags
- 24-31 Unused, zero

TADR is used in chain mode. At the start of a chain transfer, a tag will be read from TADR, and TADR will be incremented by the size of the tag, which is either 2 or 4 words.

## Chain Mode Tag Format
- 0-23  Start address of transfer in RAM
- 30    IRQ - raises IQE interrupt in DICR2 when all words in this tag are transferred
- 31    End of transfer, raises transfer completion interrupt
- 32-55 Size of transfer in words
  
... Optionally 2 words to transfer after tag ...

If `Dn_CHCR.8` is set, 2 words after the tag in memory `(TADR + 8)` will be transferred first. This is used by SIF0 to transfer a DMAtag to the EE's DMAC.
Only the SIF0, SIF1, and SPU1 channels can use tag mode. SIF0 and SIF1 use it exclusively, but it is unknown whether anything uses chain mode on SPU1.

### `1F8010F0h` DPCR - DMA Priority/Enable
Each 4 bits correspond to a channel.

Bits 0-2 are priority, where 0=highest and 7=lowest. If two channels have the same priority, priority is determined by channel number, where higher channels have higher priority.
Bit 3 is a channel enable, 0=disable, 1=enable.

- 0-3   Channel 0 (MDECin)
- 4-7   Channel 1 (MDECout)
- 8-11  Channel 2 (SIF2)
- 12-15 Channel 3 (CDVD)
- 16-19 Channel 4 (SPU1)
- 20-23 Channel 5 (PIO)
- 24-27 Channel 6 (OTC)
- 28-31 Channel 'C' (CPU priority. Bit 31 has no effect)

Initial value on PS2 reset is `0x07777777`. Initial value on PS1 reset is `0x07654321`.

### `1F801570h` DPCR2 - DMA Priority/Enable 2
Works similarly to DPCR.
  
- 0-3   Channel 7   (SPU2)
- 4-7   Channel 8   (DEV9)
- 8-11  Channel 9   (SIF0)
- 12-15 Channel 10  (SIF1)
- 16-19 Channel 11  (SIO2in)
- 20-23 Channel 12  (SIO2out)
- 24-27 Channel 'U' (USB DMA, controlled through USB registers)
- 28-31 Unknown

### `1F8010F4h` DICR - DMA Interrupt Register
- 0-6   Controls channel 0-6 completion interrupts in bits 24-30.
  + When 0, an interrupt only occurs when the entire transfer completes.
  + When 1, interrupts can occur for every slice and linked-list transfer.
  + No effect if the interrupt is masked by bits 16-22.
- 7-14  Unused
- 15    Bus error flag. Raised when transferring to/from an address outside of RAM. Forces bit 31 to be set.
- 16-22 Channel 0-6 interrupt mask. If enabled, channels cause interrupts as per bits 0-6.
- 23    Master channel interrupt enable.
- 24-30 Channel 0-6 interrupt flags. Writing 1 clears a flag.
  + __IMPORTANT:__ The flag only gets raised if the interrupt is enabled! INTRMAN relies on this behavior.
- 31    Master interrupt flag. When set, IRQ 3 is sent to the IOP's INTC.
  + DICR.31 = `DICR.15 | (DICR.23 && (DICR.24-30 || DICR2.24-29)`

### `1F801574h` DICR2 - DMA Interrupt Register 2
  0-12  Interrupt on tag bit. Corresponds to channels 0-12, but only bits 4, 9, and 10 can be set - this corresponds to SPU1, SIF0, and SIF1.
  + When set, an interrupt is raised on an IRQ tag when the tag transfer completes.
  + __NOTE:__ This works independently of the channel's interrupt mask, so a tag interrupt can still happen even when the transfer interrupt is disabled.
- 13-15 Unused
- 16-21 Channel 7-12 interrupt mask. If enabled, channels cause interrupts upon transfer completion.
- 22-23 Unused
- 24-29 Channel 7-12 interrupt flags. Works same as DICR interrupt flags, including being masked by DICR.23
- 30-31 Unused

### `1F801578h` DMACEN - Global DMA Enable
- 0
  + 0=All transfers disabled
  + 1=All transfers enabled

This register can be safely accessed while DMA is ongoing.

### `1F80157Ch` DMACINTEN - Global DMA Interrupt Control
  0     When 0, all channel interrupts disabled. Master interrupt flag is 0 in all cases but bus error interrupts.
  1     When 1, DMA interrupts disabled - IRQ 3 is never sent to INTC. Does not affect master interrupt flag.
