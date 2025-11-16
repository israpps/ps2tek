---
sort: 6
--- 

# SIF RPC System Servers

System server IDs have bit 31 set (e.g. 80000000h | . Known system IDs are listed here. Note that games can install custom servers with any possible ID, as long as bit 31 is not set, so those are excluded from the list.

```note
Arcade PS2s boards are based on a different SCE sdk version than the one used to build the builtin software of the retail PS2 bios.

so keep it in mind, as some modules, like FILEIO, will have a diferent rpc server ID
```

System Server IDs
ID | Service | Module | Notes
-- | ------- | ------ | -----
`80000001h` | File I/O | FILEIO | 
`80000003h` | IOP Heap Allocation | FILEIO | 
`80000006h` | Module/ELF Loader | LOADFILE | 
`80000100h` | Pad | PADMAN | 
`80000101h` | Pad extension? | PADMAN | 
`80000400h` | Memory cards | MCSERV | 
`80000592h` | CDVD Init | CDVDFSV | 
`80000593h` | CDVD S commands | CDVDFSV | 
`80000595h` | CDVD N commands | CDVDFSV | 
`80000597h` | CDVD SearchFile | CDVDFSV | 
`8000059Ah` | CDVD Disk Ready | CDVDFSV | 
`80000701h` | LIBSD Remote | SDRDRV | not in BIOS 
`80000901h` | MTAP Port Open | MTAPMAN |  not in BIOS
`80000902h` | MTAP Port Close | MTAPMAN | 
`80000903h` | MTAP Get Connection | MTAPMAN | 
`80000904h` | MTAP Unknown | MTAPMAN | 
`80000905h` | MTAP Unknown | MTAPMAN | 
`80001400h` | EyeToy | EYETOY | not in BIOS


NAMCO System246/456 Server IDs
ID | Service | Module | Notes
-- | ------- | ------ | -----
`0x76500003` | JV SIF | ACJVSIF | 
`0x76500001` | ACIOPM | ACMEME  | exposes the interfaces provided by acjv, acmem, acram, and acsram to the EE using SIF RPC.
`0x76500002` | ACCDVDE | ACCDVDE | exposes the interfaces provided by accdvd
`0x1000` | UNKNOWN |  | 
`0xFFFF` | ARCTC | ACRTC | exposes the real-time clock provided by cdvdman
