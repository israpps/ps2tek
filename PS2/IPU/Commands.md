---
sort: 2
---

# IPU Commands

## `00h` BCLR - Clear Input FIFO
- 0-7   BP - Bitstream start

BCLR clears all data in the input FIFO. The bitstream pointer is set to BP.

## `01h` IDEC - Slice Decode
- 0-5   FB - Bitstream skip
- 16-20 QSC - Quantizer step
- 24    DTD - When set, IDEC decodes DT
- 25    SGN - When set, output RGB is decremented by 128 for each channel. Underflow wraps around
- 26    DTE - Dither enable. Only applicable for RGB16
- 27    OFM - Output format
  + 0=RGB32
  + 1=RGB16

## `02h` BDEC - Macroblock Decode
- 0-5   FB - Bitstream skip
- 16-20 QSC - Quantizer step
- 25    DT - Frame type
  + 0=Frame
  + 1=Field
- 26    DCR - When set, DC prediction value is reset
- 27    MBI - Intra bit
  + 0=Non-intra macroblock
  + 1=Intra macroblock

## 03h VDEC - VLC Decode
- 0-5   FB - Bitstream skip
- 26-27 TBL - VLC table to read from
  + 0=Macroblock Increment (MBI)
  + 1=Macroblock Type
  + 2=Motion Code
  + 3=DMVector

## `04h` FDEC - Fixed-length Decode
- 0-5   FB - Bitstream skip

## `05h` SETIQ - Set Quantization Table
- 0-5   FB - Bitstream skip
- 27    IQM - Matrix type
  + 0=Intra matrix
  + 1=Non-intra matrix

## 06h SETIQ - Set Quantization Table
  No option bits

## `07h` CSC - Color Space Conversion
- 0-10  MBC - Macroblocks to decode
- 26    DTE - Dither enable. Only applicable for RGB16
- 27    OFM - Output format
  + 0=RGB32
  + 1=RGB16

## `08h` PACK - 32-bit -> 4-bit/16-bit Color Conversion
- 0-10  MBC - Macroblocks to convert
- 26    DTE - Dither enable
- 27    OFM - Output format
  + 0=INDX4
  + 1=RGB16

## `09h` SETTH - Set Alpha Thresholds
- 0-8   TH0 - Transparent alpha threshold
- 16-24 TH1 - Translucent alpha threshold

