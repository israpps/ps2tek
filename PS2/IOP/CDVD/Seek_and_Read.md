---
sort: 3
---
# CDVD Reads and Seeks

## Seeking
If a read command is called, a seek must be performed. An IRQ is NOT raised when the seek finishes during a read command.

When a seek begins, if the drive is currently not spinning, it takes 333 ms for the drive to spin and finish the seek.

Otherwise, one of three seek modes is possible:
- Contiguous read: When the seek delta is very small or zero, seek time = block_timing * delta
- Fast seek: When seek delta < 14764 for DVD reads and < 4371 for CD reads, seek time = ~30 ms
- Full seek: Seek time = ~100 ms

block_timing (in IOP cycles) can be found by the following formula:

$$ Block\text{ }Timing = \frac{IOP\text{ }CLOCK * block\text{ }size}{read\text{ }speed} $$

Where `IOP_CLOCK` is ~36,864,000 Hz. read_speed for CD reads is `24 * 153600`. For DVD reads read_speed = `4 * 1382400`.
Unknown what delta is needed for a contiguous read (`8/16` is used for CD/DVD respectively in PCSX2).

## Reads
The time needed to read a single sector is the block_timing formula above. Once one sector has been read, the CDVD DMA channel can store the data in memory and allow the CDVD drive to continue.

When all sectors have been read, a CDVD IRQ is raised. Successful reads seem to raise both bits 1 AND 0 of CDVD I_STAT?

