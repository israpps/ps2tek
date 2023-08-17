---
sort: 3
---

# SIO2 PS2 Memcard Filesystem

The PS2 uses a double-indirect indexed FAT filesystem to keep track of clusters. It also has support for ECC and bad block detection, depending on MCMAN version.

## Superblock Format
 Byte  | Name
:----: | ---- 
0-27    |Magic string. Should contain `Sony PS2 Memory Card Format `
28-39   |Version string. Has the format `1.x.0.0`, where x is the minor version
40-41   |Size in bytes of a page. Default `512`
42-43   |Pages per cluster. Default `2`
44-45   |Pages per erase block. Default `16`
46-47   |Set to `-256`. Doesn't seem to be used
48-51   |Total clusters in the card. Default `8192`
52-55   |Offset of the first allocatable cluster, in cluster units. Immediately after the FAT
56-59   |Offset of the cluster after the last allocatable cluster, relative to the first
60-63   |Offset of the root directory cluster, relative to the first. Should be `0`
64-67   |Backup erase block. Should be the last block in the card, default `1023`
68-71   |Second backup block, should be the second-last block in the card
80-207  |Array of `32` indirect FAT cluster indices. On a standard card, only one indirect cluster is used
208-335 |Array of `32` bad blocks, which cannot be used. `-1` indicates no entry
384     |Memory card type. Should be `2`, indicating a PS2 memory card
385     |Card flags. Default `52h`</br>Bit 0=ECC support if set</br>Bit 3=Card has bad blocks</br>Bit 4=Erased blocks have bits set to 0 if set

The superblock is located in the first page in the memory card.
