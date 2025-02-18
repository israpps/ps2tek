---
sort: 1
---

# File Structure

The BIOS consists of dozens of separate files. These files are indexed and accessed through ROMDIR.

Each ROMDIR entry has the following format.

```c
  struct romdir_entry
  {
    char name[10]; //File name, must be null terminated
    ushort ext_info_size; //Size of the file's extended info in EXTINFO
    uint file_size; //Size of the file itself
  }
```

First four entries in an SCPH-39001 ROMDIR:
```
0x00002740: 52455345 54000000 00000C00 40270000      RESET
0x00002750: 524F4D44 49520000 00005400 D0050000      ROMDIR
0x00002760: 45585449 4E464F00 00000000 80070000      EXTINFO
0x00002770: 524F4D56 45520000 00000000 10000000      ROMVER
...
```

To find the start of `ROMDIR`, look for the first occurrence of `RESET` in the BIOS.
To find a specific file in the BIOS, find its entry in ROMDIR and then add the file sizes of all previous files to get the starting address.