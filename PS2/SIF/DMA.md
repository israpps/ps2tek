---
sort: 2
---
# SIF DMA

During SIF initialization and after bootup the EE and IOP can use DMA to send data to each other. This is achieved using a set of fifos (First In First Out) located in the SIF, refered to as SIF0 and SIF1.
Assume that the EE wants to send data to the IOP:

- The EE writes the data it wants to send to RAM, alongside with respective DMA tags
- It calls SifSetDma to initialize a SIF1 transfer to the IOP
- The DMAC starts writing qwords to the SIF1 fifo
- After the data is transfered, the IOP starts a SIF1 transfer and reads the data in the fifo
- Finally an interrupt is asserted on DICR/DICR2 and if that mask is also set, an IOP
interrupt is triggered.

```note
if a SIF0/SIF1 transfer is started on either component and the fifo is empty the transfer continues running until the respective fifo starts filling with data. In addition this process relies hevily on interrupts and DICR flag writing to work.
```