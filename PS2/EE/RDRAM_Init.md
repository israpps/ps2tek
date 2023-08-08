---
sort: 4
---
# EE RDRAM initialization

During the early boot stages, the BIOS writes to a pair of registers; MCH_DRD and MCH_RICM used to initialize RDRAM. Not much is known about these registers. The following logic used in emulators:

## Read
```c
  case 0x1000f430:
  {
    return 0;
  }
  case 0x1000f440:
  {
    uint8_t SOP = (MCH_RICM >> 6) & 0xF;
    uint8_t SA = (MCH_RICM >> 16) & 0xFFF;
    if (!SOP)
    {
        switch (SA)
        {
        case 0x21:
            if (rdram_sdevid < 2)
            {
                rdram_sdevid++;
                return 0x1F;
            }
            return 0;
        case 0x23:
            return 0x0D0D;
        case 0x24:
            return 0x0090;
        case 0x40:
            return MCH_RICM & 0x1F;
        }
    }
    return 0;
  }
```

## Write
```c
  case 0x1000f430:
  {
      uint8_t SA = (data >> 16) & 0xFFF;
      uint8_t SBC = (data >> 6) & 0xF;

      if (SA == 0x21 && SBC == 0x1 && ((MCH_DRD >> 7) & 1) == 0)
          rdram_sdevid = 0;

      MCH_RICM = data & ~0x80000000;
      break;
  }
  case 0x1000f440:
  {
      MCH_DRD = data;
      break;
  }
```
