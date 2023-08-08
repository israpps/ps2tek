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


## Source Chain Tag ID

```
0    refe    MADR=DMAtag.ADDR
             TADR+=16
             tag_end=true

1    cnt     MADR=TADR+16 (next to DMAtag)
             TADR=MADR (next to transfer data)

2    next    MADR=TADR+16
             TADR=DMAtag.ADDR

3    ref     MADR=DMAtag.ADDR
             TADR+=16

4    refs    MADR=DMAtag.ADDR
             TADR+=16

5    call    MADR=TADR+16
             if (CHCR.ASP == 0)
               ASR0=MADR+(QWC*16)
             else if (CHCR.ASP == 1)
               ASR1=MADR+(QWC*16)
             TADR=DMAtag.ADDR
             CHCR.ASP++

6    ret     MADR=TADR+16
             if (CHCR.ASP == 2)
               TADR=ASR1
               CHCR.ASP--
             else if (CHCR.ASP == 1)
               TADR=ASR0
               CHCR.ASP--
             else
               tag_end=true

7    end     MADR=TADR+16
             tag_end=true
```

When tag_end=true, the transfer ends after QWC has been transferred.

## Dest Chain Tag ID
```
0    cnt     MADR=DMAtag.ADDR

1    cnts    MADR=DMAtag.ADDR

7    end     MADR=DMAtag.ADDR
             tag_end=true
```

## DMA Resuming

If a transfer starts in source chain mode and QWC > 0, the DMAC assumes that the TAG field of CHCR was the last read DMAtag. This means that if the tag ID is REFE or END, the channel will stop after QWC has been transferred.

Sony's movie-playing library requires this behavior, as it uses MADR to determine the current movie buffer position.
