---
sort: 4
---

# GIF PATH3 Masking

PATH1 and PATH2 transfer to the GS without any internal buffering. However, PATH3 has a 16-quadword GIF FIFO.

When PATH3 masking is enabled, data sent by PATH3 will reside in the FIFO until the mask is lifted. This is useful for queueing texture transfers while other paths are sending geometry. GTA: San Andreas, Lemmings, and some other games rely on this.

Furthermore, Wallace and Gromit at Project Zoo will enable the mask and start a GIF DMA transfer, expecting it to finish. Emulating PATH3 masking without emulating the GIF FIFO will cause it to hang on a black screen, as the DMA channel is unable to write any data.
