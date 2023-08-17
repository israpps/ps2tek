---
sort: 4
---

# SIO2 Controller Commands

SIO2 communicates with the controller by sending sequences of bytes and receiving replies from the controller.

Note that the reply comes immediately after the appropriate byte has been sent, more specifically the reply bytes are not queued.

Packets have a three byte header followed by an additional 2, 6 or 18 bytes of additional command and controller data

(like button states, vibration motor commands, button pressures, etc.).

## Packet header
- Command: `01` + command byte + `00`
- Reply: `FF` + depends on command + `5A`
- Length: 3

The first byte is the peripheral byte sent by SIO2 and is always `01` for controllers. The reply is always `FF`. The command byte tells the peripheral which command to execute.

All commands reply with `F3` except from commands `42` and `43` which reply with the mode byte (more details below). The third byte is always `00` and its reply is always `5A`
Below is a list of known controller commands.

## `41h` - Query Masked Mode
Command: header + `5A` + `5A` + `5A` + `5A` + `5A` + `5A`
  
If in digital mode or a nonstandard controller:
- Reply: header reply + zero bytes
  
If in analog or DS2 mode:
- Reply: header reply + 3 mask bytes + `00` + `00` + `5A` (last byte is always 0x5A)
- Length: `8`


This command is used to find out what buttons are included in poll responses. The controller can be configured (through command 0x4F) to respond with

more or less information about each button with each poll. If the controller is in digital mode (0x41) this commands returns zeroes.

Only works when the controller is already in configuration mode.

## `42h` - Read Data
Command: header + variable zero bytes
If in digital mode or a nonstandard controller:
- Reply: header reply (returns mode byte) + 2 button state bytes
- Length: 4

If in analog or DS2 mode:
- Reply: header reply (returns mode byte) + 2 button state bytes + 4 analog button state bytes + (optional) 12 button pressure bytes
- Length: 8 or 20 depending if analog button pressures are requested.

The format of controller inputs varies based not only on what is connected, but also what its current mode is. See the respective controller pages for their input formats.

The high nibble of the mode byte indicates the mode (0x4 is digital, 0x7 is analog, 0xF config / escape?), while the lower nibble is how many 16 bit words follow the header.

Known mode bytes returned by header are presented below:
- `12h`   Mouse
- `23h`   NeGcon
- `41h`   DualShock Digital
- `73h`   DualShock Analog
- `79h`   DualShock 2 (analog+pressure)
- `F3h`   In config mode

## `43h` - Enter/Exit Config Mode
- Outside config mode:
  + Command: header + 1 config byte (1=enter config) + zero bytes
  + Reply: header reply (with mode byte) + same as Read Data (but without pressure values in DS2 mode)
  + Length: Same as Read Data
  
- Inside config mode:
  + Command: header + 1 config byte (0=exit config) + zero bytes
  + Reply: header reply (with mode byte) + 6 zero bytes
  + Length: 8

Config mode allows the controller state to be changed. When the controller is not in config mode, only commands 42h and 43h work.

When the controller is outside of config mode this command behaves the same as 42. If the command is in config mode is command can only be used to exit config mode.

## `45h` - Query Model and Mode
- Command: header + 5A + 5A + 5A + 5A + 5A + 5A
- Reply: header reply + model + 02 + analog + 02 + 01 + 00
- Length: 8

model=`01h` for Dualshock and `03` for Dualshock 2
analog=`01h` if the controller is in analog mode and `00h` if in digital mode.

## `46h` - Query Act
- Command: header + index + 5A + 5A + 5A + 5A + 5A
  
- If index=0
   + Reply: header reply + 00 + 00 + 01 + 02 + 00 + 0A
  
- If index=1
   + Reply: header reply + 00 + 00 + 01 + 01 + 01 + 14

- Length: 8

This command is always issued twice in a row, and appears to be retrieving a 10 byte constant of over those two calls.

It is always called in a sequence of 46h 46h 47h 4Ch 4Ch. Only works after the controller is in config mode.

## `47h` - Query Comb
- Command: header + 00 + 5A + 5A + 5A + 5A + 5A
- Reply: header reply + 00 + 00 + 02 + 00 + 01 + 00
- Length: 8

Unknown purpose.

## `4Ch` - Query Mode
- Command: header + index + 5A + 5A + 5A + 5A + 5A
- Reply: header reply + 00 + 00 + 00 + val + 00 + 00
- Length: 8
  
if index=0, val=4
if index=1, val=7

Unknown purpose.

## `4Dh` - Vibration Toggle
- Command: header + `aa` + `bb` + `cc` + `dd` + `ee` + `ff`
- Reply: header reply + .. old rumble values ..
- Length: 8

This command configures rumble, but it is unknown exactly how. Bytes aa-ff are used for this.

On reset, rumble values are all FFh.

