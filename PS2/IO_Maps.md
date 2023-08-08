---
sort: 2
---

# I/O Maps

## EE Map
### EE Timers
- `100000xxh`       Timer 0
- `100008xxh`        Timer 1
- `100010xxh`        Timer 2
- `100018xxh`        Timer 3
### Image Processing Unit (IPU)
- `10002000h` `8h`     IPU Command
- `10002010h` `4h`     IPU Control
- `10002020h` `4h`     IPU bit pointer control
- `10002030h` `8h`     Top of bitstream
- `10007000h` `10h`    Out FIFO (read)
- `10007010h` `10h`    In FIFO (write)
### Graphics Interface (GIF)
- `10003000h` `4h`     GIF_CTRL - Control register
- `10003010h` `4h`     GIF_MODE - Mode setting
- `10003020h` `4h`     GIF_STAT - Status
- `10003040h` `4h`     GIF_TAG0 - Bits 0-31 of tag before
- `10003050h` `4h`     GIF_TAG1 - Bits 32-63 of tag before
- `10003060h` `4h`     GIF_TAG2 - Bits 64-95 of tag before
- `10003070h` `4h`     GIF_TAG3 - Bits 96-127 of tag before
- `10003080h` `4h`     GIF_CNT - Transfer status counter
- `10003090h` `4h`     GIF_P3CNT - PATH3 transfer status counter
- `100030A0h` `4h`     GIF_P3TAG - Bits 0-31 of PATH3 tag when interrupted
- `10006000h` `10h`    GIF FIFO
### DMA Controller (DMAC)
- `100080xxh`        VIF0 - channel 0
- `100090xxh`        VIF1 - channel 1
- `1000A0xxh`        GIF - channel 2
- `1000B0xxh`        IPU_FROM - channel 3
- `1000B4xxh`        IPU_TO - channel 4
- `1000C0xxh`        SIF0 - channel 5
- `1000C4xxh`        SIF1 - channel 6
- `1000C8xxh`        SIF2 - channel 7
- `1000D0xxh`        SPR_FROM - channel 8
- `1000D4xxh`        SPR_TO - channel 9
- `1000E000h` `4h`     D_CTRL - DMAC control
- `1000E010h` `4h`     D_STAT - DMAC interrupt status
- `1000E020h` `4h`     D_PCR - DMAC priority control
- `1000E030h` `4h`     D_SQWC - DMAC skip quadword
- `1000E040h` `4h`     D_RBSR - DMAC ringbuffer size
- `1000E050h` `4h`     D_RBOR - DMAC ringbuffer offset
- `1000E060h` `4h`     D_STADR - DMAC stall address
- `1000F520h` `4h`     D_ENABLER - DMAC disabled status
- `1000F590h` `4h`     D_ENABLEW - DMAC disable
### Interrupt Controller (INTC)
- `1000F000h` `4h`     INTC_STAT - Interrupt status
- `1000F010h` `4h`     INTC_MASK - Interrupt mask
Subsystem Interface (SIF)
- `1000F200h` `4h`     MSCOM - EE->IOP communication
- `1000F210h` `4h`     SMCOM - IOP->EE communication
- `1000F220h` `4h`     MSFLAG - EE->IOP flags
- `1000F230h` `4h`     SMFLAG - IOP->EE flags
- `1000F240h` `4h`     Control register
### Privileged GS registers
- `12000000h` `8h`     PMODE - various PCRTC controls
- `12000010h` `8h`     SMODE1
- `12000020h` `8h`     SMODE2
- `12000030h` `8h`     SRFSH
- `12000040h` `8h`     SYNCH1
- `12000050h` `8h`     SYNCH2
- `12000060h` `8h`     SYNCV
- `12000070h` `8h`     DISPFB1 - display buffer for output circuit 1
- `12000080h` `8h`     DISPLAY1 - output circuit 1 control
- `12000090h` `8h`     DISPFB2 - display buffer for output circuit 2
- `120000A0h` `8h`     DISPLAY2 - output circuit 2 control
- `120000B0h` `8h`     EXTBUF
- `120000C0h` `8h`     EXTDATA
- `120000D0h` `8h`     EXTWRITE
- `120000E0h` `8h`     BGCOLOR - background color
- `12001000h` `8h`     GS_CSR - control register
- `12001010h` `8h`     GS_IMR - GS interrupt control
- `12001040h` `8h`     BUSDIR - transfer direction
- `12001080h` `8h`     SIGLBLID - signal
### Misc registers
- `1000F180h` `1h`     KPUTCHAR - Console output
- `1000F430h` `4h`     MCH_DRD - RDRAM initialization
- `1000F440h` `4h`     MCH_RICM

## IOP Map
### Subsystem Interface (SIF)
- `1D000000h` `4h`     MSCOM - EE->IOP communication
- `1D000010h` `4h`     SMCOM - IOP->EE communication
- `1D000020h` `4h`     MSFLAG - EE->IOP flags
- `1D000030h` `4h`     SMFLAG - IOP->EE flags
- `1D000040h` `4h`     Control register
### CDVD Drive
- `1F402004h` `1h`     Current N command
- `1F402005h` `1h`     N command status (R)
- `1F402005h` `1h`     N command params (W)
- `1F402006h` `1h`     Error
- `1F402007h` `1h`     Send BREAK command
- `1F402008h` `1h`     CDVD I_STAT - interrupt register
- `1F40200Ah` `1h`     Drive status
- `1F40200Fh` `1h`     Disk type
- `1F402016h` `1h`     Current S command
- `1F402017h` `1h`     S command status
- `1F402018h` `1h`     S command params
### Interrupt Control
- `1F801070h` `4h`     I_STAT - Interrupt status
- `1F801074h` `4h`     I_MASK - Interrupt mask
- `1F801078h` `1h`     I_CTRL - Global interrupt disable
### DMA registers
- `1F80108xh`        MDECin - channel 0
- `1F80109xh`        MDECout - channel 1
- `1F8010Axh`        SIF2 (GPU) - channel 2
- `1F8010Bxh`        CDVD - channel 3
- `1F8010Cxh`        SPU2 Core0 - channel 4
- `1F8010Dxh`        PIO - channel 5
- `1F8010Exh`        OTC - channel 6
- `1F80150xh`        SPU2 Core1 - channel 7
- `1F80151xh`        DEV9 - channel 8
- `1F80152xh`        SIF0 - channel 9
- `1F80153xh`        SIF1 - channel 10
- `1F80154xh`        SIO2in - channel 11
- `1F80155xh`        SIO2out - channel 12
  
- `1F8010F0h` `4h`     DPCR - DMA priority control
- `1F8010F4h` `4h`     DICR - DMA interrupt control
- `1F801570h` `4h`     DPCR2 - DMA priority control 2
- `1F801574h` `4h`     DICR2 - DMA priority control 2
- `1F801578h` `4h`     DMACEN - DMA global enable
- `1F80157Ch` `4h`     DMACINTEN - DMA global interrupt control
### IOP Timers
- `1F80110xh`        Timer 0
- `1F80111xh`        Timer 1
- `1F80112xh`        Timer 2
- `1F80148xh`        Timer 3
- `1F80149xh`        Timer 4
- `1F8014Axh`        Timer 5
### Serial Interface (SIO2)
- `1F808200h` `40h`    SEND3 buffer
- `1F808240h` `20h`    SEND1/2 buffers
- `1F808260h` `1h`     In FIFO
- `1F808264h` `1h`     Out FIFO
- `1F808268h` `4h`     SIO2 control
- `1F80826Ch` `4h`     RECV1
- `1F808270h` `4h`     RECV2
- `1F808274h` `4h`     RECV3
### Sound Processing Unit (SPU2)
- `1F900000h` `180h`   Core0 Voice 0-23 registers
- `1F900190h` `4h`     Key ON 0/1
- `1F900194h` `4h`     Key OFF 0/1
- `1F90019Ah` `2h`     Core attributes
- `1F90019Ch` `4h`     Interrupt address H/L
- `1F9001A8h` `4h`     DMA transfer address H/L
- `1F9001ACh` `2h`     Internal transfer FIFO
- `1F9001B0h` `2h`     AutoDMA status
- `1F9001C0h` `120h`   Core0 Voice 0-23 start/loop/next addresses
- `1F900340h` `4h`     ENDX 0/1
- `1F900344h` `2h`     Status register
  
> above addresses repeat for Core1 starting at 1F900400h
  
- `1F900760h` `2h`     Master Volume Left
- `1F900762h` `2h`     Master Volume Right
- `1F900764h` `2h`     Effect Volume Left
- `1F900766h` `2h`     Effect Volume Right
- `1F900768h` `2h`     Core1 External Input Volume Left
- `1F90076Ah` `2h`     Core1 External Input Volume Right
