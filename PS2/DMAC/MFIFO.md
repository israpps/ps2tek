---
sort: 3
---

# DMAC MFIFO

Scratchpad memory is much faster than main memory, but the scratchpad is a measly 16 KB, only enough for 1024 quadwords of data.

The Memory FIFO (MFIFO) works around this issue by automatically transferring data sent via SPR_FROM to a user-defined buffer in main memory. The D_RBSR and D_RBOR registers define the size and location of this buffer. SPR_FROM's MADR is automatically set to the next read position in the buffer, wrapping around when the end is reached.

Either the VIF1 DMA channel or GIF DMA channel can be the drain channel, reading from the buffer when SPR_FROM transfers data to it. When a new tag is read, the drain channel's TADR is set to D_RBOR | (TADR & D_RBSR), as is MADR if QWC is non-zero and the tag ID is not REF/REFE/REFS.

When TADR equals SPR_FROM's MADR, the drain channel is stalled, and an MFIFO empty interrupt is triggered. If the tag ID is REF/REFE/REFS and QWC is non-zero, an interrupt is still sent but the channel is not stalled.

At first, it may seem odd to use MFIFO, because the extra transfer is slower than just interacting with VIF1/GIF DMA directly. However, VIF1 and the GIF have a tendency to stall for long periods of time when transferring data to the GS because of VU1 transferring data to PATH1. MFIFO allows the EE to queue up data without worrying about GIF stalls and managing DMA buffers, which in turn allows the EE to do other things more efficiently.
