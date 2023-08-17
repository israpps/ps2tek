---
sort: 1
---

# CDVD I/O Ports
## `1F402004h` Current N command (R/W)

Write to this register to send an N command. For a list of N commands, see [CDVD N Commands](./NCMD.md)

## `1F402005h` N command status (R)
- 0     Error (1=error occurred)
- 1     Unknown/unused
- 2     DEV9 device connected (1=HDD/network adapter connected)
- 3     Unknown/unused
- 4     Test mode
- 5     Power off ready
- 6     Drive status (1=ready)
- 7     Busy executing NCMD
CDVDMAN reads bits 6 and 7 to know when the device is ready to receive a command - bit 6 must be on and bit 7 must be off.

## `1F402005h` N command param (W)
Send parameters for an N command here. This must be done BEFORE the N command has been sent via 1F402004h.

## `1F402006h` CDVD error (R)
Any non-zero value indicates an error? Unknown what kind of errors are possible and what their values are.

## `1F402007h` BREAK
Writing any value to this register sends a BREAK command to the CDVD drive, stopping execution of the current N command.

## `1F402008h` CDVD I_STAT (R=Status, W=Acknowledge)
Status = Read I_STAT (1=Reason for IRQ)
Acknowledge = Write I_STAT (1=Clear bit)
- 0     Data ready?
- 1     (N?) Command complete
- 2     Power off pressed
- 3     Disk ejected
- 4     BS_Power DET?
- 5-7   Unused

When a CDVD IRQ is raised on I_STAT, this register shows the reason for the interrupt. Bit 0 seems to be raised when a read command completes, but I'm not certain about this...

Unknown if bit 1 only applies to N commands, although this appears to be the case.

## `1F40200Ah` CDVD drive status (R)
- 0     Tray status (1=open)
- 1     Spindle spinning (1=spinning)
- 2     Read status (1=reading data sectors)
- 3     Paused
- 4     Seek status (1=seeking)
- 5     Error (1=error occurred)
- 6-7   Unknown

## `1F40200Bh` Sticky drive status (R)
Exact same as 0xA, except the bits are "sticky" - the bits here are ORed with all previous drive states.

CDVDMAN compares 0xA and 0xB to know if the tray status has changed. SCMD 0x5 sets this register to reg 0xA's value.

## `1F40200Fh` CDVD disk type (R)
- `00h`  No disc
- `01h`  Detecting
- `02h`  Detecting CD
- `03h`  Detecting DVD
- `04h`  Detecting dual-layer DVD
- `05h`  Unknown
- `10h`  PS1 CD
- `11h`  PS1 CDDA
- `12h`  PS2 CD
- `13h`  PS2 CDDA
- `14h`  PS2 DVD
- `FDh`  CDDA (Music)
- `FEh`  DVDV (Movie disc)
- `FFh`  Illegal

## `1F402016h` Current S command (R/W)
Write to this register to send an S command. For a list of S commands, see [CDVD S Commands](./SCMD.md)

## `1F402017h` S command status (R)
- 0-5   Unknown
- 6     Result data available (0=available, 1=no data)
- 7     Busy

## `1F402017h` S command params (W)
Parameters must be sent BEFORE the S command is sent.


## `1F402018h` S command result (R)
When an S command has finished executing, read the result here. Some S commands may require multiple reads.
