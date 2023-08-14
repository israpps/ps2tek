---
sort: 1
---

# IPU I/O Registers

## `10002000h` IPU_CMD - IPU Send Command
### Write
- 0-27  Option - dependent on command
- 28-31 Code - the actual command
  
### Read
- 0-31  Result of FDEC/VDEC command
- 63    Busy

IPU_CMD is where all commands are sent.

```note
Strange quirk: if data is in the IPU through DMA but no commands have been sent, IPU_CMD will contain the first 32 bits of the bitstream. Certain games, e.g., Theme Park Rollercoaster, rely on this behavior to decode the bitstream before sending the first FDEC command.
```

## `10002010h` IPU_CTRL - IPU Control/Status
- 0-3   IFC - Size of data in input FIFO in quadwords
- 4-7   OFC - Size of data in output FIFO in quadwords
- 8-13  CBP - Coded block pattern, written to by BDEC/IDEC
- 14    ECD - Error code detected
- 15    SCD - Start code detected
- 16-17 IDP - Intra DC precision
  + 0=8 bits
  + 1=9 bits
  + 2=10 bits
- 20    AS - Scan pattern for BDEC
  + 0=Zigzag
  + 1=Alternate
- 21    IVF - Intra VLC format
  + 0=MPEG1-compatible
  + 1=Intra macro block
- 22    QST - Quantize step for BDEC
  + 0=Linear
  + 1=Nonlinear
- 23    MP1 - If set, treats bitstream as MPEG1. Otherwise, MPEG2
- 24-26 Picture type for VDEC
  + 1=I-picture
  + 2=P-picture
  + 3=B-picture
  + 4=D-picture
- 30    RST - Writing 1 to this resets the whole IPU
- 31    Busy
When a command is sent, ECD and SCD are cleared to 0. A reset triggers an IPU interrupt if a command is currently executing.

## `10002020h` IPU_BP - Bitstream Position
- 0-6   BP - Position within the 128-bit quadword being decoded in bits
- 8-11  IFC - Size of data in input FIFO in quadwords
- 16-17 FP - Size of data in internal buffer in quadwords

This register is used to determine the amount of unprocessed DMA data currently in the IPU. Sony's FMV library uses this to know what to set MADR to when IPU processing is halted and resumed.

## `10002030h` IPU_BP - Bitstream Position
- 0-31  Next 32 bits in the bitstream
- 63    Busy/not enough data

The "busy" bit is set when less than 32 bits are in the FIFO. FMV libraries rely on this undocumented behavior.
