---
sort: 4
---

# CDVD S Commands

The results of an S command can be read one at a time from 1F402017h. All units are in bytes.

## `03h` Subcommand
Params:
- 0     Subcommand number

This is a prefix byte. Subcommands are listed below in the format of `03h:XXh`.

### `03h:00h` MechaconVersion
Params: None.

Result:
- 0-3   MECHACON version

Unknown what the bytes mean. Newer versions of CDVDMAN do checks on them of some sort.
PCSX2 uses `00020603h` as the version.

## `05h` UpdateStickyFlags
Params: None.

Result:
- 0     Update status (0=successful, 1=busy)

Sets `1F40200Bh` to the value of `1F40200Ah`. Used internally by CDVDMAN for detecting if a disk has been inserted.

## `08h` ReadRTC
Params: None.

Result:
- 0     Zero
- 1     Second
- 2     Minute
- 3     Hour
- 4     Zero
- 5     Day
- 6     Month
- 7     Year
Returns the current time stored on the RTC, in BCD format.

The RTC is mostly just used by the BIOS. Notably, Metal Gear Solid 3 requires the RTC to boot. This is likely an anti-piracy feature.

## `09h` WriteRTC
Params:
- 0     Ignored
- 1     Second
- 2     Minute
- 3     Hour
- 4     Ignored
- 5     Day
- 6     Month
- 7     Year
Overwrites the RTC's time in BCD format.

## `05h` UpdateStickyFlags
Params: None.

Result:
- 0     Update status (0=successful, 1=busy)

Sets `1F40200Bh` to the value of `1F40200Ah`. Used internally by CDVDMAN for detecting if a disk has been inserted.


## `15h` ForbidDVD
Params: None.

Blocks access to the DVD drive. PCSX2 writes `0x5` to result


## `40h` OpenConfig
Params: None.

Opens the DVD drive configuration. Dobiestation returns zero


## `41h` ReadConfig
Params: None.

Reads the previously opened configuration file. Output is four 32bit words


## `43h` CloseConfig
Params: None.

Closes the opened config file
