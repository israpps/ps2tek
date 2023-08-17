---
sort: 2
---
# CDVD N Commands

Parameters are in units of bytes and are little-endian. All N commands raise IRQ2 (bit 1 of CDVD I_STAT).

## `00h` NOP
## `01h` NOPsync
Params: None.

These commands do nothing? They do raise an IRQ upon their "completion".

## `02h` Standby
Params: None.

Returns the read position to sector 0 and sets the drive status to PAUSED.
Possibly also spins the drive if it's not spinning already?

## `03h` Stop
Params: None.

Returns the read position to sector 0 and stops the drive.
Seems to have a 166 ms delay?

## `04h` Pause
Params: None.

Unknown what effect this has, from the perspective of emulation.

## `05h` Seek
Params:
- 0-3   Sector position

Moves the read position to the indicated parameter.

## `06h` ReadCd
Params:
- 0-3   Sector position
- 4-7   Sectors to read
- 10    Block size (1=2328 bytes, 2=2340 bytes, all others=2048 bytes)

Performs a CD-style read. Seems to raise bit 0 of CDVD I_STAT upon completion?

## `08h` ReadDvd
# Params:
- 0-3   Sector position
- 4-7   Sectors to read
Performs a DVD-style read, with a block size of 2064 bytes. The format of the data is as follows:

offset | size | description |
:----: | :--: | ----------- |
0      | 1    | Volume number + 0x20 |
1      | 3    | Sector number - volume start + 0x30000, in big-endian. |
4      | 8    | ? (all zeroes) |
12     | 2048 | Raw sector data |
2060   | 4    | ? (all zeroes) |

## `09h` GetToc
Params: None? 

Fetches the ToC from the disk, with a block size of 2064 bytes.
