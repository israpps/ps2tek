---
sort: 2
---

# BIOS Boot Process

Upon reset, both the EE and IOP begin executing at `BFC00000h`. The BIOS checks which CPU is executing by checking `COP0.PRid` (register 15). If `PRid >= 59h`, the EE boot code is executed. Else, the IOP boot code is executed.

## EE boot process

- The EE clock speed is measured. (SCPH-10000 uses COP0.Count; SCPH-39001 counts HBLANKs using one of the timers. Unknown what other BIOSes use.)
- The memory controller is initialized, giving access to the 32 MB of RDRAM.
- The EE kernel is copied into RAM starting at virtual address `80000000h`. The BIOS jumps to the entry point at `80001000h`.
- The TLB and PGIF handler are initialized and various hardware components are reset.
- EENULL, an idle loop thread, is loaded into `00081FC0h`.
- SIF DMA is initialized. The EE writes to an SIF register and waits for a reply from the IOP.
- The EELOAD module is loaded, which in turn loads OSDSYS.

OSDSYS, once loaded, is responsible for bringing up the "Sony Computer Entertainment" screen and the browser.

## IOP boot process
- Various hardware registers are initialized, and `IOPBOOT` is loaded.
- `IOPBOOT` finds `SYSMEM` and `LOADCORE`, loads them into memory, and executes their entry points.
- `LOADCORE` boots all of the IOP modules in the BIOS, starting with `EXCEPMAN`.
- At some point, SIF is initialized. The IOP waits for the EE to send a message and replies over another SIF register.
- Once all modules have been loaded, the IOP enters an infinite loop, waiting for the EE to send commands.
