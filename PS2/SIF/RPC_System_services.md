---
sort: 6
--- 

# SIF RPC System Servers

System server IDs have bit 31 set (e.g. 80000000h | . Known system IDs are listed here. Note that games can install custom servers with any possible ID, as long as bit 31 is not set, so those are excluded from the list.


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