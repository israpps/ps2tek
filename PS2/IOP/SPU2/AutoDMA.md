---
sort: 1
---

# SPU2 AutoDMA

## `1F9001B0h+(core*400h)` SPU_ADMA_CTRL
- 0: On core0, 1=start ADMA write/ADMA busy. No effect on core1
- 1: Same as above, but only works on core1
- 2: Start ADMA read on this core (1=start ADMA read). Unknown how ADMA reads work

ADMA streams signed stereo 16-bit PCM audio to the MEMIN region, bypassing almost all SPU2 processing. In other words, ADMA allows games to stream raw audio without compression or special effects - this feature is usually used for music.

ADMA uses the normal DMA interface, and it is not possible to have both ADMA and a normal SPU DMA transfer active at the same time.

MEMIN and Data Format
```
  2000h-20FFh    Core0 left buffer 0
  2100h-21FFh    Core0 left buffer 1
  2200h-22FFh    Core0 right buffer 0
  2300h-23FFh    Core0 right buffer 1
  
  2400h-24FFh    Core1 left buffer 0
  2500h-25FFh    Core1 left buffer 1
  2600h-26FFh    Core1 right buffer 0
  2700h-27FFh    Core1 right buffer 1
```

A full ADMA block is 1024 bytes, where the first 512 bytes are left channel samples, and the other half is the right channel. MEMIN is double-buffered, allowing the mixer hardware to read from one buffer while ADMA writes to another free buffer. Some games do upload blocks smaller than 1024 bytes, which partially fills the free buffer.

