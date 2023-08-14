
# VIF Commands

##  Command Format

```
31        24 23       16 15                     0
-------------------------------------------------
|    CMD    |    NUM    |       IMMEDIATE       |
-------------------------------------------------
```

Bit 31 is an interrupt bit - when set, a VIF interrupt is generated after the command executes, and the VIF stalls on the following command except for the MARK command.

NUM and IMMEDIATE are command-specific and are not always used.

## Command List

### `00h` NOP

Does nothing. Mainly used for timing PATH3 masking and aligning the DMA stream for certain commands.

### `01h` STCYCL
Sets the CYCLE register to IMMEDIATE. In particular, CYCLE.CL is set to bits 0-7 and CYCLE.WL is set to bits 8-15.

The CYCLE register is used for skipping/filling writes for UNPACK.

### `02h` OFFSET (VIF1)
Sets the OFST register to bits 0-9 of IMMEDIATE. This is used for VIF1 double buffering.

DBF (the double buffering flag) is also cleared to 0 in STAT, and BASE is set to TOPS.

### `03h` BASE (VIF1)
Sets the BASE register to bits 0-9 of IMMEDIATE. This is used for VIF1 double buffering.

### `04h` ITOP
Sets the ITOP register to bits 0-9 of IMMEDIATE. ITOP can be read by the XITOP instruction on the VU.

### `05h` STMOD
Sets the MODE register to bits 0-1 of IMMEDIATE. This is used for addition decompression in UNPACK.

### `06h` MSKPATH3 (VIF1)
Sets the VIF-side PATH3 mask to bit 15 of IMMEDIATE. When PATH3 masking is enabled, the mask is applied to the next data block for PATH3.

### `07h` MARK
Sets the MARK register to IMMEDIATE.

### `10h` FLUSHE
Stalls the VIF until the VU is finished executing a microprogram.

### `11h` FLUSH (VIF1)
Stalls VIF1 until the VU is finished executing a microprogram and PATH1 and PATH2 are not active.

### `13h` FLUSHA (VIF1)
Stalls VIF1 until the VU is finished executing a microprogram, PATH1 and PATH2 are not active, and there is no pending transfer request for PATH3.

### `14h` MSCAL
Starts a microprogram on the VU at the given address `IMMEDIATE*8`. If the VU is currently active, MSCAL stalls until the VU is finished before executing a new microprogram.

### `15h` MSCALF (VIF1)
Same as MSCAL, but also waits for PATH1 and PATH2 to not be active before starting a microprogram.

### `17h` MSCNT
Starts microprogram execution starting at the VU's TPC register - this usually means the instruction right after the end of the previous microprogram.

If the VU is currently active, MSCNT stalls like MSCAL.

### `20h` STMASK
Sets the MASK register to the next 32-bit word in the stream. This is used for UNPACK write masking.

### `30h` STROW
Sets the R0-R3 row registers to the next 4 32-bit words in the stream. This is used for UNPACK write filling.

### `31h` STCOL
Sets the C0-C3 column registers to the next 4 32-bit words in the stream. This is used for UNPACK write filling.

### `4Ah` MPG
Loads NUM*8 bytes into VU micro memory, starting at the given address IMMEDIATE*8. If the VU is currently active, MPG stalls until the VU is finished before uploading data.

If NUM is 0, then 2048 bytes are loaded.

### `50h` DIRECT (VIF1)
Transfers IMMEDIATE quadwords to the GIF through PATH2. If PATH2 cannot take control of the GIF, the VIF stalls until PATH2 is activated.

If IMMEDIATE is 0, 65,536 quadwords are transferred.

### `51h` DIRECTHL (VIF1)
Same as DIRECT, except DIRECTHL is not able to interrupt PATH3 in IMAGE mode and stalls if PATH3 is transferring in IMAGE mode.

### `60h`-`7Fh` UNPACK

Decompresses data in various formats to the given address in bits 0-9 of IMMEDIATE multiplied by 16.

If bit 14 of IMMEDIATE is set, the decompressed data is zero-extended. Otherwise, it is sign-extended.

If bit 15 of IMMEDIATE is set, TOPS is added to the starting address. This is only applicable for VIF1.

Bits 0-3 of CMD determine the type of UNPACK that occurs. See VIF UNPACK for details.

Bit 4 of CMD performs UNPACK write masking if set.

