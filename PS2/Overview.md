---
sort: 0
---

# PS2 Overview
## PS2 Hardware
- Emotion Engine (EE) - Main CPU.
  + DMAC - Intelligent DMA controller. Used for accessing most peripherals on the EE.
  + EE Timers - Four 16-bit timers.
  + Graphics Interface (GIF) - Transmits graphical data to the GS.
  + Vector Interface (VIF) - Decompresses vector data, uploads microprograms to the VUs, and sends graphical data to the GIF.
  + Vector Units (VUs) - Custom DSPs used to process vertex data, physics calculations, and other related tasks.
  + Image Processing Unit (IPU) - MPEG1/MPEG2 video decoder.
  + Scratchpad - 16 KB of fast memory.
  
- Graphics Synthesizer (GS) - Fixed-function GPU. Designed to draw polygons very, very fast.
  + PCRTC - Renders GS output to a television screen. Can be programmed to work on a variety of TVs, including NTSC and PAL.
  
- Input/Output Processor (IOP) - Duplicate of the PS1's CPU. Used for slower input/output tasks and for PS1 backwards compatibility.
  + IOP DMA - Same channels as on the PS1, along with additional channels exclusive to PS2 mode.
  + IOP Timers - Same timers as on the PS1, with three additional 32-bit counters.
  + CDVD Drive - Reads disc media. Also responsible for MagicGate decryption.
  + SIO2 - Serial ports used to read controllers and memory cards.
  + SPU2 - Sound processor. Similar to the PS1 SPU with added features.
  + DEV9 - Expansion port. Used mainly for the PS2 HDD and Network Adapter.
  + USB
  + FireWire
  
- Subsystem Interface (SIF) - Allows the EE and IOP to communicate with each other.
