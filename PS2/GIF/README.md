# Graphics Interface (GIF)
The GIF sends textures and geometry to the GS. It takes data from three different PATHs:

- `PATH1`: VU1 via XGKICK instruction. Highest priority.
- `PATH2`: VIF1 via DIRECT/DIRECTHL. Medium priority.
- `PATH3`: GIF DMAC channel (channel 2). Lowest priority.

Only one PATH may run at a time. Under normal conditions, when one PATH stops, the highest priority queued PATH will begin transfer.

GIF Reference

{% include list.liquid all=true %}
