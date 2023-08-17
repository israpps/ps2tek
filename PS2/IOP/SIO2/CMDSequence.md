---
sort: 5
---

# SIO2 Command Sequence

The PS2 supports both DualShock and DualShock 2 controllers natively. However, certain games require DS2.

## Input Format
```
  Cmd     Reply
  42h     ID_lo
  ??h     ID_hi
  ??h     Buttons_lo
  ??h     Buttons_hi
  ---- transfer stops here if digital ----
  ??h     Analog right joystick, y-axis (00h = upmost, 80h = centered, FFh = downmost)
  ??h     Analog right joystick, x-axis (00h = leftmost, 80h = centered, FFh = rightmost)
  ??h     Analog left joystick, y-axis (see above)
  ??h     Analog right joystick, x-axis (see above)
  ---- transfer stops here if not DS2 ----
  ??h     D-pad right pressure (00h = no pressure, FFh = max pressure)
  ??h     D-pad left pressure
  ??h     D-pad up pressure
  ??h     D-pad down pressure
  ??h     Triangle pressure
  ??h     Circle pressure
  ??h     Cross pressure
  ??h     Square pressure
  ??h     L1 pressure
  ??h     R1 pressure
  ??h     L2 pressure
  ??h     R2 pressure
```
  
## Button bits
> 0=pressed, 1=released

bit   | button
:---: | :----:
  0   |Select
  1   |L3
  2   |R3
  3   |Start
  4   |D-pad Up
  5   |D-pad Right
  6   |D-pad Down
  7   |D-pad Left
  8   |L2
  9   |R2
  10  |L1
  11  |R1
  12  |Triangle
  13  |Circle
  14  |Cross
  15  |Square
