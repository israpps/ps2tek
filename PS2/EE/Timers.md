---
sort: 12
---

# EE Timers

The EE has four hardware timers. They are all functionally equivalent, though T0 and T1 have an additional register used when an SBUS interrupt occurs.
T3 is reserved by the BIOS for alarm functions. All other timers may be used freely.

`10000000h + N*800h` TN_COUNT
- 0-15  Current counter
  
16-bit counter incremented according to the settings in TN_MODE. Writes set the counter to the given value.

`10000010h + N*800h` TN_MODE
- 0-1   Clock
  + 0=Bus clock (~147 MHz)
  + 1=Bus clock / 16
  + 2=Bus clock / 256
  + 3=HBLANK
- 2     Gate enable
- 3     Gate type
  + 0=HBLANK
  + 1=VBLANK
- 4-5   Gate mode
  + 0=Count while gate not active
  + 1=Reset counter when gate goes from low to high
  + 2=Reset counter when gate goes from high to low
  + 3=Reset counter for high<->low gate transitions
- 6     Clear counter when it reaches compare value
- 7     Timer enable
- 8     Compare interrupt enable - trigger interrupt when COUNT==COMP
- 9     Overflow interrupt enable - trigger interrupt when COUNT goes from FFFFh to 0000h
- 10    Compare interrupt flag
  + W: Clears flag
  11    Overflow interrupt flag
  + W: Clears flag
Timer interrupts are edge-triggered: an interrupt will only be sent to the EE if either interrupt flag goes from 0 to 1.
Harry Potter and the Prisoner of Azkaban relies on this behavior, as it does not clear the interrupt flags in the timer handler but rather in a different part of the code. Not emulating this will cause the game to crash by reading from NULL.

`10000020h + N*800h` TN_COMP
- 0-15  Compare value

`10000030h + N*800h` TN_HOLD
- 0-15  Counter value when an SBUS interrupt occurs
  
Only exists for T0 and T1.


Vertical and horizontal timings are presented below.

## Vertical Timings
- PAL:  312 scanlines per frame (VBOFF: 286 | VBON: 26)
- NTSC: 262 scanlines per frame (VBOFF: 240 | VBON: 22)

## Horizontal Timings
- PAL:  9436 BUSCLK cycles per scanline
- NTSC: 9370 BUSCLK cycles per scanline
