---
sort: 11
---

# EE COP1 Floating Point Format

COP1, along with the rest of the PS2, only supports single-precision floats. The PS2 uses IEEE 754 floats with the following format:
```
  31-30---------23-22---------------------------------------------0
  |si|   exponent  |                mantissa                      |
  -----------------------------------------------------------------
```
`si` is the sign bit, and the mantissa is the fractional component. The float is calculated as follows.
```c
  num = (si ? -1 : 1) * 2 ^ (exponent - 127) * (1.mantissa)
```
`1.mantissa` means the number 1 and all the fractional bits following the decimal.
For example, `3F800000h` becomes 1.0f.

## IEEE 754 Differences
There are major differences between the PS2 floating-point format and standard IEEE 754, used in most modern computers.

NaNs and Infinities do not exist on the PS2. In IEEE 754, any number with the exponent field set to 255 is a NaN/Infinity. On the PS2, it is treated as a normal value. This means the upper range of PS2 floats is greater than on IEEE 754.

Denormals, numbers with the exponent field set to 0, do not exist on the PS2. For every arithmetic operation, a denormal is automatically truncated to zero.

On the PS2, the rounding mode is always forced to round-towards-zero. The PS2 uses a quirky version where the least-significant-bit may vary after a round, and the details on this are not well understood.
