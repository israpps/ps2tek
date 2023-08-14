---
sort: 1
---

# VIF I/O Registers

> Most VIF registers are read-only unless specified otherwise.

## `10003800h`/`10003C00h` VIFn_STAT
- 0-1   VPS - VIF command status
  + 0=Idle
  + 1=Waiting for data following command
  + 2=Decoding command
  + 3=Decompressing/transferring data
- 2     VEW - VU is executing microprogram
- 3     VGW - Stalled waiting for GIF (VIF1 only)
- 6     MRK - MARK detected
- 7     DBF - Double buffer flag (VIF1 only)
  + 0=TOPS = BASE
  + 1=TOPS = BASE + OFST
- 8     VSS - Stalled after STOP was sent to FBRST
- 9     VFS - Stalled after force break was sent to FBRST
- 10    VIS - Stalled on interrupt ibt
- 11    INT - Interrupt bit detected
- 12    ER0 - DMAtag mismatch error (don't know what this means)
- 13    ER1 - Invalid VIF command was sent
- 23    FDR - FIFO direction (VIF1 only)
  + 0=Memory -> VIF FIFO
  + 1=VIF FIFO -> Memory
- 24-28 FQC - Amount of quadwords in FIFO
  + Max 8 for VIF0, 16 for VIF1

## `10003810h`/`10003C10h` VIFn_FBRST
- 0     RST - Reset VIF including contents of FIFO when written to
- 1     FBK - Force break the VIF, causing an immediate stall
- 2     STP - STOP the VIF, stalling it after it finishes the current command
- 3     STC - Stall cancel. Clears VSS, VFS, VIS, INT, ER0, and ER1 in VIFn_STAT
This is a write-only register used to control VIF resets and stalls.

## `10003820h`/`10003C20h` VIFn_ERR
- 0     MII - Disable interrupt bit stalls and interrupts if set
- 1     ME0 - Disable DMAtag mismatch error and stall if set
- 2     ME1 - Disable invalid command error and stall if set
ME0 should always be set to 1. ME1 should be set to 1 when doing UNPACK V3-16.

## `10003830h`/`10003C30h` VIFn_MARK
- 0-15  Most recently set MARK value

This register can be written to by the EE. Writes clear the MRK flag in VIFn_STAT.

## `10003840h`/`10003C40h` VIFn_CYCLE
- 0-7   CL - Cycle length
- 8-15  WL - Write cycle length

## `10003850h`/`10003C50h` VIFn_MODE
- 0-1   Addition mode used for UNPACK

## `10003860h`/`10003C60h` VIFn_NUM
- 0-7   Amount of untransferred data in MPG/UNPACK

## `10003870h`/`10003C70h` VIFn_MASK
- 0-31  Write mask matrix

## `10003880h`/`10003C80h` VIFn_CODE
- 0-15  IMMEDIATE value processed most recently
- 16-23 NUM value processed most recently
- 24-31 CMD value processed most recently

This register contains the last processed command or if the VIF was stalled while processing a command, the command currently being processed.

## `10003890h`/`10003C90h` VIFn_ITOPS
- 0-9   ITOPS value

## `10003CA0h` VIF1_BASE
- 0-9   BASE value

## `10003CB0h` VIF1_OFST
- 0-9   OFST value

## `10003CC0h` VIF1_TOPS
- 0-9   TOPS value

## `100038D0h`/`10003CD0h` VIFn_ITOP
- 0-9   ITOP value

## `10003CE0h` VIF1_TOP
- 0-9   TOP value

## `10003900h`-`10003930h`/`10003D00h`-`10003D30` VIFn_RN

Array of 4 32-bit values used for row filling data. Each value is stored at 100039N0h/10003DN0h.

## `10003940h`-`10003970h`/`10003D40h`-`10003D70` VIFn_CN

Same as VIFn_RN, except for column filling data.
