---
sort: 4
---

# DMAC Interrupts

Upon any transfer completion, the DMAC will raise the channel's stat flag in D_STAT. When (stat & mask) != 0, the DMAC asserts an INT1 signal to the EE.

INT1 may also be asserted when MFIFO is empty, a stall occurs, or a bus error occurs. The bus error is not maskable via D_STAT.

NOTE: INT0 (INTC) has priority over INT1. If INT0 and INT1 are asserted at the same time, the INT0 interrupt will be processed first.
