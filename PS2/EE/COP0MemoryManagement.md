---
sort: 7
---

# EE COP0 Memory Management

## Virtual Memory Basics
Virtual memory is a mechanism that allows processors to arbitrarily map a virtual address to a physical address. This is accomplished using a Memory Management Unit (MMU). For example, a program might access address 1000h. With virtual memory, this address could map to any physical address, such as 1000h, 20000h, 424000h, and so on. Virtual address 1000h might also not be mapped to anything, in which case accessing it causes an error. The PS2 and most modern OSes do not map addresses near NULL, so that if a program uses a NULL pointer, it can crash immediately rather than corrupt program data.

## MIPS Memory Segments
The MIPS architecture splits the memory space in half for the user and kernel, and kernelspace is further partitioned into various segments.

Name | Range | Description
---- | ----- | -----------
kuseg | `00000000h`-`7FFFFFFFh` | User, TLB-mapped                  |
kseg0 | `80000000h`-`9FFFFFFFh` | Kernel, directly-mapped, cached   |
kseg1 | `A0000000h`-`BFFFFFFFh` | Kernel, directly-mapped, uncached |
ksseg | `C0000000h`-`DFFFFFFFh` | Supervisor, TLB-mapped            |
kseg3 | `E0000000h`-`FFFFFFFFh` | Kernel, TLB-mapped                |

<div class="flash mt-3 flash-warn">
  
Accessing a privileged region without the necessary privileges raises an Address Error exception.
For <code>kseg0</code> and <code>kseg1</code>, the physical address retrieved is simply (address & <code>1FFFFFFFh</code>).
  
</div>


## Translation-Lookaside Buffer (TLB)
The EE uses a TLB, a cache of virtual->physical mappings, for memory management. The TLB contains 48 entries split into even and odd pages. A TLB entry has the following format:
- 1       V0 - Even page valid. When not set, the memory referenced in this entry is not mapped.
- 2       D0 - Even page dirty. When not set, writes cause an exception.
- 3-5     C0 - Even page cache mode.
  + 2=Uncached
  + 3=Cached
  + 7=Uncached accelerated
- 6-25    PFN0 - Even page frame number.
- 33      V1 - Odd page valid.
- 34      D1 - Odd page dirty.
- 35      C1 - Odd page cache mode.
- 38-57   PFN1 - Odd page frame number.
- 63      S - Scratchpad. When set, the virtual mapping goes to scratchpad instead of main memory.
- 64-71   ASID - Address Space ID.
- 76      G - Global. When set, ASID is ignored.
- 77-95   VPN2 - Virtual page number / 2.
  + Even pages have a VPN of (VPN2 * 2) and odd pages have a VPN of (VPN2 * 2) + 1
- 109-120 MASK - Size of an even/odd page.

The units of PFN0/PFN1 and VPN2 depend on MASK. For example, if MASK indicates a size of 4 KB, then if PFN0/PFN1 are 10h/11h and VPN2 is 8h, the virtual address of the even page is (8h * 2 * 4096) or 10000h, and the virtual address of the odd page is ((8h * 2) + 1) * 4096 or 11000h.
The physical addresses of the even/odd pages are also 10000h/11000h respectively. Therefore, an access to 10500h will touch physical address 10500h.

## $0 - COP0.Index
- 0-5   TLB entry to access with TLBR/TLBWI

## $1 - COP0.Random
- 0-5   TLB entry to access with TLBWR

Random decrements after every instruction that is executed. It has an upper bound of 47 and a lower bound of the value in Wired.

## $2/$3 - COP0.EntryLo0/EntryLo1
- 0       G - Global.
- 1       V - Page valid. When not set, the memory referenced in this entry is not mapped.
- 2       D - Page dirty. When not set, writes cause an exception.
- 3-5     C - Page cache mode.
  + 2=Uncached
  + 3=Cached
  + 7=Uncached accelerated
- 6-25    PFN - Page frame number.
- 31      S - Scratchpad. Only applicable for EntryLo0.

EntryLo0/EntryLo1 modify the lower part of the TLB as given for the even page and odd page respectively.
The Global bit in the TLB entry is only set if both EntryLo0 and EntryLo1 have their G bit set.

##$5 - COP0.PageMask
- 13-24   Page size
  + 000h=4 KB
  + 003h=16 KB
  + 00Fh=64 KB
  + 03Fh=256 KB
  + 0FFh=1 MB
  + 3FFh=4 MB
  + FFFh=16 MB

## $6 - COP0.Wired
- 0-5   Lower bound of Random

## $10 - COP0.EntryHi
- 0-7     ASID
- 13-31   VPN2
