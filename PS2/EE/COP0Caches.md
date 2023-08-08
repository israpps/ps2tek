---
sort: 8
---

# EE COP0 Caches

## Cache Basics
As technology has progressed, CPU speeds have far outpaced memory speeds. An access to main RAM on the PS2 takes around 40 EE cycles, and on a modern computer, it can be hundreds of cycles! CPU caches alleviate this problem. They are smaller but faster buffers of memory that the CPU attempts to access when fetching instructions or loading/storing data.
If the CPU finds what it is looking for in the cache, it is called a cache hit. If the CPU has to access main memory, it is called a cache miss.

Modern caches are very complex and have multiple levels, but the EE's caches are rather simple, having only a single level and two ways.

### EE Cache Structure
The instruction cache and data cache are split into 128 and 64 cache lines respectively. Each line has two 64-byte (4-quadword) ways, which means the icache and dcache are 16 KB and 8 KB respectively.
Each way has a tag which stores metadata about the way, including, for instance, if the way has valid data in it.
The structure of the tags for the icache and dcache is as follows.
```
    icache
    -------------------------------------------
    |V|R|                 PFN                 |
    -------------------------------------------
  
    dcache
    -------------------------------------------
    |D|V|R|L|             PFN                 |
    -------------------------------------------
  ```
- V=Valid. If set, the way contains valid data.
- R=LRF bit. Used for the LRF cache replacement algorithm.
- D=Dirty. Set on writes. If set when the way is reloaded, the data is first stored to main memory before loading new data.
- L=Locked. Prevents the way from ever being reloaded. Only allowed for a single way in a line.

Each cache line is indexed by virtual address. Here is how a virtual address is used:
```
    icache
    31---------------------------------14-13-------------6-5---------0
    |                 PFN                 |     index      | offset  |
    ------------------------------------------------------------------
  
    dcache
    31------------------------------------13-12----------6-5---------0
    |                 PFN                    |    index    | offset  |
    ------------------------------------------------------------------
```
Note how the dcache index is 6-bit, whereas the icache index is 7-bit.

The index is used to get the cache line to access. The offset is used to, of course, offset into the 64-byte region.

### Cache Misses and Replacement Algorithm
When accessing the cache, the EE checks both ways in a line by comparing the address's PFN with the PFNs in the ways' tags. If both lookups fail, a cache miss has occurred, and new data needs to be loaded into the cache.

The EE uses a Least Recently Filled (LRF) algorithm to determine which way to load data into. If either way does not have the Valid bit set, the EE sets the Valid bit in the first way that does not have it and loads data into it. If both ways are full, the EE takes the XOR of the LRF bits in the tags. The result is the index of the way it uses. The EE then flips the LRF bit in the selected way.

If a way in the data cache has the Locked bit set, the EE will always reload the other way. This allows important data to remain in the cache indefinitely.

Four quadwords are loaded into a way at a time. Since the EE bus is 128-bit, this means a cache miss has a penalty of a minimum of four bus cycles (eight EE cycles). However, main memory is much slower - ~40 EE cycles is closer to reality, though the exact number is nondeterministic due to the nature of RDRAM.

### Cache Coherency
Because the caches are separate from main memory, it is possible for the contents of the caches to be different from the contents of main memory.

This is not an issue when simply accessing memory. However, other hardware components such as the DMAC cannot read from the cache. When transferring or receiving data from hardware peripherals, the program is expected to maintain cache coherency by flushing the cache, which forces the cache to synchronize its contents with main memory.

Sony's SDK correctly maintains cache coherency in most cases, so most games are none the wiser. Certain games are so buggy, however, that they require cache emulation to work.
Some examples:

- Dead or Alive 2: Re-uses the same buffer for sending and receiving data in a library that communicates with a custom sound module on the IOP. Without cache emulation, the IOP will overwrite data in the send/receive buffer, preventing the game from booting.
- Ice Age 2: The Meltdown: Function returns a pointer to a stack array used to send data to the IOP. Astute C programmers will recognize this as undefined behavior. The game immediately flushes the cache after calling this function, so the ensuing corruption only occurs in the cache, and the data in main memory is protected.
- Savage Skies: Similar to IA2. Allocates a VIF DMA buffer on the stack, then starts a transfer without waiting for it to end and returns from a function. Without cache emulation, the buffer is corrupted and the game is unable to go in-game.
- WRC 4: Notably requires icache emulation. Main executable is a small decompressor that decompresses a larger executable. The decompressed ELF is large enough to overlap with the decompressor in memory. Without icache emulation, the decompressor is destroyed.
