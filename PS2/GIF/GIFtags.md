---
sort: 2
---

# GIFtags

The basic unit of GIF transfer data is the GIF packet. Each packet is split into one or more primitives. Every primitive must be preceded by a GIFtag.

## GIFtag Format
- 0-14    NLOOP - Data per register to transfer
- 15      EOP - End of packet
- 16-45   Unused
- 46      Enable PRIM field
- 47-57   Data to be sent to GS PRIM register if GIFtag.46 == 1
- 58-59   Data format
  + 0=PACKED
  + 1=REGLIST
  + 2=IMAGE
  + 3=IMAGE
- 60-63   NREGS - Number of registers
  + 0=16 registers
- 64-127  Register field, 4 bits each

When `NLOOP == 0`, all fields are ignored except EOP and no further processing is done.
When `GIFtag.46 == 0`, an idle cycle is inserted before processing begins.
Registers are handled in little-endian order; i.e. bits 64-67 are processed first, then 68-71, and so on.

```note
The GS Q register is initialized to 1.0f when reading a GIFtag.
```
