---
sort: 1
---
# SIO2 Registers

<div class="flash mt-3 flash-warn">
  <!-- <%= octicon "alert" %> -->
  <svg class="octicon" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 16 16" width="16" height="16"><path fill-rule="evenodd" d="M8.22 1.754a.25.25 0 00-.44 0L1.698 13.132a.25.25 0 00.22.368h12.164a.25.25 0 00.22-.368L8.22 1.754zm-1.763-.707c.659-1.234 2.427-1.234 3.086 0l6.082 11.378A1.75 1.75 0 0114.082 15H1.918a1.75 1.75 0 01-1.543-2.575L6.457 1.047zM9 11a1 1 0 11-2 0 1 1 0 012 0zm-.25-5.25a.75.75 0 00-1.5 0v2.5a.75.75 0 001.5 0v-2.5z"></path></svg>
A good portion of SIO2 is a mystery. Having no documentation whatsoever, its behavior has to be inferred from how programs use it.
</div>


### `1F808200h`-`1F80823Fh` SIO2_SEND3 - Command Parameters
- 0-1   Port
- 8-16  Fake command length, not used by SIO2
- 18-24 Real command length (counting peripheral byte)

Other Unknown

SEND3 is an array of up to 16 different SIO2 commands. A command consists of a peripheral byte that selects to what the command is being sent, a command sent to the peripheral, and an arbitrary amount of data.

## Known peripheral bytes:
- `01h` - Controller
- `21h` - Multitap
- `61h` - Infrared
- `81h` - Memory card

### `1F808240h`-`1F80825Fh` SIO2_SEND1/SEND2 - Port1/2 Control?

Unknown purpose.

When bit 2 of the address is set, SEND2 is accessed. Otherwise, SEND1 is accessed.

### `1F808260h` SIO2_FIFOIN - Data Write
A one-byte register used to upload commands to SIO2. The SIO2in DMA channel also writes to this register.

### `1F808264h` SIO2_FIFOOUT - Data Read
Used to read replies and data from SIO2 peripherals after a command is sent. SIO2out reads from this register.

### `1F808268h` SIO2_CTRL - Control Register

Bit 0 seems to start the command transfer. An SIO2 interrupt is raised after touching this bit, presumably when the command has completed.

Bits 2 and 3 reset SIO2, preparing it for another transfer.

Rest of the bits are unknown. They probably control bandwidths and interrupt masking, at the very least.

The SIO2MAN module in the BIOS sets this register to 3BCh on a reset.

### `1F80826Ch` SIO2_RECV1 - Response Status 1 (R)
Set after a transfer, indicating if the peripheral is connected.
If `(RECV1 & F000h) == 1000h` after a memory card command, the memory card is connected. Else, disconnected.
If `(RECV1 & 2000h) == 0` after a pad command, the pad is connected.

Known value for a disconnected peripheral is `1D100h`. Known value for a connected peripheral is `1100h`.

### `1F808270h` SIO2_RECV2 - Response Status 2 (R)
Read by `PADMAN`. Always equal to `0xF?`

### `1F808274h` SIO2_RECV3 - Response Status 3 (R)
Unknown.

### `1F808280h` SIO2_ISTAT? - Interrupt Flags?
SIO2MAN's interrupt handler reads from this and writes to it the value read. Not known what any of the bits represent.

SIO2MAN Program Flow For Transfer
1. Write CTRL | 0Ch to CTRL.
2. Write data to SEND1 and SEND2, then write data to SEND3.
3. Write data to DATAIN if applicable, then start SIO2in and SIO2out DMA transfers if applicable.
4. Write CTRL | 01h to CTRL, then wait for an SIO2 interrupt.
5. After interrupt, read RECV1, RECV2, and RECV3, then read DATAOUT if applicable.

