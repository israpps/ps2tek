---
sort: 1
---

# SIF Registers

```note
EE base is at `1000F200h`, IOP base is at `1D000000h`.
```

## `1000F200h`/`1D000000h` `SIF_MSCOM` (Only writable by EE)
## `1000F210h`/`1D000010h` `SIF_SMCOM` (Only writable by IOP)
- 0-31  Value

These registers could be used for generic communication, but in the PS2 they are used to tell the other side the address of its DMA receive buffer. For example, the EE places its SIF0 receive address in MSCOM so that the IOP knows where to transfer data to, and the opposite is true for SMCOM.

## `1000F220h`/`1D000020h` SIF_MSFLG (IOP writes mask)
## `1000F230h`/`1D000030h` SIF_SMFLG (EE writes mask)
- 0-31  Value

These registers are used like semaphores. The following values are used:
- `10000h`: SIF DMA/hardware initialized
- `20000h`: SIFCMD initialized
- `40000h`: IOP has finished booting (sent by EESYNC)

If the EE sends `20000h` to MSFLG, the IOP can read this and clear MSFLG by writing `20000h` to it. The opposite is true for SMFLG.

## `1000F240h`/`1D000040h` `SIF_CTRL`
- 1:     Always 1?
- 8:     Always 1 for EE, 0 for IOP?
- 28-31: Always `0xF`?
- Other Unknown
Very little is known about this register.

## `1000F260h`/`1D000060h` SIF_BD6
- 0-31  Unknown
Nothing is known about this register, other than that it does get accessed during initialization.