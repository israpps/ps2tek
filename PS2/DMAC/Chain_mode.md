---
sort: 2
---

# DMAC Chain Mode

The DMAC can intelligently chain DMA transfers together. When a transfer starts in chain mode, the channel will transfer QWC quadwords and then read a DMAtag.

In source chain mode, the DMAtag comes from TADR. In destination chain mode, the DMAtag comes from the peripheral the channel is reading from.

## DMAtag
- 0-15    QWC to transfer
- 16-25   Unused
- 26-27   Priority control
  + 0=No effect
  + 1=Reserved
  + 2=Priority control disabled (D_PCR.31 = 0)
  + 3=Priority control enabled (D_PCR.31 = 1)
- 28-30   Tag ID
- 31      IRQ
- 32-62   ADDR field (lower 4 bits must be zero)
- 63      Memory selection for ADDR (0=RAM, 1=scratchpad)
- 64-127  Data to transfer (only if Dn_CHCR.TTE==1)

When both IRQ and Dn_CHCR.TIE are set, the transfer ends after QWC has been transferred.

When Dn_CHCR.TTE is on, bits 64-127 are transferred BEFORE QWC.

The effects of the tag ID vary depending on if the channel is in source chain or dest chain mode.
