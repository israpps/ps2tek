---
sort: 3
---

# EE Interrupt Controller (INTC)

The EE has two separate interrupt signals: INT0 (raised by INTC) and INT1 (raised by DMAC).

## `1000F000h` INTC_STAT - Interrupt status register (R=Status, W=Acknowledge)
## `1000F010h` INTC_MASK - Interrupt mask register (R/W)

Status: Read INTC_STAT (1=IRQ raised)

Acknowledge: Write INTC_STAT (0=No effect 1=Clear bit)

Mask: Write INTC_MASK (0=No effect, 1=Reverse)
```
  0     IRQ0   GS interrupt
  1     IRQ1   SBUS
  2     IRQ2   VBLANK start
  3     IRQ3   VBLANK end
  4     IRQ4   VIF0
  5     IRQ5   VIF1
  6     IRQ6   VU0
  7     IRQ7   VU1
  8     IRQ8   IPU
  9     IRQ9   Timer 0
  10    IRQ10  Timer 1
  11    IRQ11  Timer 2
  12    IRQ12  Timer 3
  13    IRQ13  SFIFO
  14    IRQ14  VU0 Watchdog
```
When (INTC_STAT & INTC_MASK), INT0 is asserted on COP0.Cause:8. When COP0.Status:8 is true, an interrupt occurs, and the EE jumps to 80000200h.
