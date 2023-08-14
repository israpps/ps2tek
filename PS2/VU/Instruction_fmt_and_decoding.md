---
sort: 3
---

# VU Instruction Format and Decoding

Each VU executes two instructions per cycle - one instruction is in the upper pipeline and the other is in the lower pipeline. A full VU instruction can be considered as a 64-bit doubleword, where the lower 32-bit word is the lower instruction and the upper word is the upper instruction.

Each pipeline is specialized and can only execute certain instructions. The upper pipeline mostly executes floating-point arithmetic instructions, and the lower pipeline mostly executes integer calculations, branches, and some specialized floating-point operations.

The upper instructions can also control various parts of execution, such as ending microprograms, loading values into the I register, and in the case of VU0, synchronization with the EE.

## Upper Instructions

```
3 3 2 2 2 2 2
1-0-9-8-7-6-5-----------------------------------0
|I|E|M|D|T|              instr                  |
-------------------------------------------------
```

Meaning of the various bits:
- I-bit: I register load. Instead of executing a lower instruction, the VU loads the 32-bit value in the lower pipeline into the I register.
- E-bit: Ends microprogram execution. The E-bit has a delay slot, much like branches.
- M-bit: Only applicable for VU0. Ends interlock on a single QMTC2.I/CTC2.I instruction, allowing the EE to continue execution.
- D-bit: Debug break. Halts the VU and sends an interrupt to the EE.
- T-bit: Debug halt. Acts similarly to D-bit.
