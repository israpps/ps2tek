---
sort: 1
---

# IOP Interrupts

Interrupt handling for the IOP is similar to its PS1 counterpart. The main differences are an additional register (`I_CTRL`) and more interrupt lines.

## `1F801070h` I_STAT - Interrupt status register (R=Status, W=Acknowledge)

## `1F801074h` I_MASK - Interrupt mask register (R/W)

Status: Read `I_STAT` (1=IRQ raised)

Acknowledge: Write `I_STAT` (0=Clear bit 1=No effect)

Mask: Read/Write `I_MASK` (0=Disabled 1=Enabled)

- 0     IRQ0   VBLANK start
- 1     IRQ1   GPU (used in PS1 mode)
- 2     IRQ2   CDVD Drive
- 3     IRQ3   DMA
- 4     IRQ4   Timer 0
- 5     IRQ5   Timer 1
- 6     IRQ6   Timer 2
- 7     IRQ7   SIO0
- 8     IRQ8   SIO1
- 9     IRQ9   SPU2
- 10    IRQ10  PIO
- 11    IRQ11  VBLANK end
- 12    IRQ12  DVD? (unknown purpose)
- 13    IRQ13  PCMCIA (related to DEV9 expansion slot)
- 14    IRQ14  Timer 3
- 15    IRQ15  Timer 4
- 16    IRQ16  Timer 5
- 17    IRQ17  SIO2
- 18    IRQ18  HTR0? (unknown purpose)
- 19    IRQ19  HTR1?
- 20    IRQ20  HTR2?
- 21    IRQ21  HTR3?
- 22    IRQ22  USB
- 23    IRQ23  EXTR? (unknown purpose)
- 24    IRQ24  FWRE (related to FireWire)
- 25    IRQ25  FDMA? (FireWire DMA?)
- 26-31 Unused/garbage

## `1F801078h` I_CTRL - Global interrupt control (R=Status and Disable, W)
- 0     Enable all interrupts
- 1-31  Unused/garbage

When bit 0=1, all IOP interrupts are enabled. Reading this register returns bit 0 AND clears it, disabling interrupts. Writing can set or reset bit 0.

## Raising Interrupts
If `I_CTRL && (I_STAT & I_MASK)`, then `COP0.Cause:8` is set. When `COP0.Status:8` is also set when this occurs, `COP0.Cause.Excode` is set to `00h` and the IOP jumps to `80000080h`, where the interrupt will be processed.
