---
sort: 3
---

# IOP Timers

### `1F801100h+N*10`h Timer 0..2 count (R/W)
- 0-15  Current value
- 16-31 Unused/garbage

### `1F801480h+(N-3)*10h` Timer 3..5 count (R/W)
- 0-31  Current value

Timers 0..5 increment automatically. 0..2 are 16-bit, and 3..5 are 32-bit. Writes set the counter to the value written.

### `1F801104h+N*10h` Timer 0..2 mode (R/W)

### `1F801484h+(N-3)*10h` Timer 3..5 mode (R/W)
- 0     Gate enable
- 1-2   Gate mode
- 3     Zero return - reset counter on interrupt
- 4     Compare interrupt enabled
- 5     Overflow interrupt enabled
- 6     Repeat interrupt - if unset, bit 10 is set to 0 after interrupt occurs.
- 7     LEVL - toggle bit 10 on IRQs if bit 6 is set.
- 8     Use external signal
  + If set:
    * Timer 0: pixel clock (13.5 MHz regardless of screen mode)
    * Timer 1/3: HBLANK
    * Others: sysclock (no effect)
- 9     Timer 2 prescaler
- 10    Interrupts enabled (R)
- 11    Compare interrupt raised (R)
- 12    Overflow interrupt raised (R)
- 13-14 Timer 4/5 prescalar
- 15-31 Unused/garbage

Writes to mode reset the count to zero and set bit 10 to 1. Reads from mode clear the two raised interrupt flags.

Prescalers adjust clockrate as follows:
- 0     normal
- 1     1/8 speed
- 2     1/16 speed
- 3     1/256 speed

### `1F801108h+N*10h` Timer 0..2 target (R/W)
- 0-15  Value
- 16-31 Unused/garbage

### `1F801488h+(N-3)*10h` Timer 3..5 target (R/W)
- 0-31  Value

When `count == target`, a compare interrupt is raised. This raises an `IRQ` in `I_STAT` if both `mode.4` and `mode.10` are enabled.
If `mode.7` (LEVL) is not set, writes to target set `mode.10` to 1.
