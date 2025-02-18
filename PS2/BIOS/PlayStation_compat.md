---
sort: 8
---

# BIOS PlayStation Compatibility

## EE Side
In PSX mode, the EE and GS work together to emulate the PSX GPU. `PS1DRV`, a standard ELF, handles this emulation.  
The main emulation loop of `PS1DRV` acts as a translation layer between PSX and GS commands. It polls `PGIF` registers to know when a command has arrived.

After OSDSYS detects a PSX disc, it loads `PS1DRV` and jumps to it. The setup for PSX mode is as follows.
- PS1DRV attempts to parse several hack databases. First, it parses an internal list of games stored in the executable. Next, if the PSX disc has a "PSD1.0.0" entry in SYSTEM.CNF, PS1DRV parses hacks from there. Finally, PS1DRV will attempt to open `B?DATA-SYSTEM/TITLE.DB` (where `?` is region letter) on both memory card ports. If one of them is opened successfully, it then parses the database. Unknown what the format of the `SYSTEM.CNF` and `TITLE.DB` hacks are.[^1]  
- Some parameters are retrieved using GetOsdConfigParam and GetGsVParam. They are used to control factors like CDROM read speed and texture filtering.
- Various system registers are reset. PS1DRV also seems to remap SPU2 registers to their original PSX address base at 1F801C00h using SSBUS writes.
- Bit 19 is set in SIF_CTRL. This is probably to assert the reset line on the IOP, switching it to PSX mode. PS1DRV waits 1000000h ticks, then unsets bit 19, which probably deasserts the reset line, allowing the IOP to run. It then waits another 1000000h ticks.
- PS1DRV enters its emulation loop and waits for GPU commands to be sent.

[^1]: `TITLE.DB`: Not all PS2 models support this, later ones (exact model range confirmation needed) droped this feature after it was exploited to create the ["Independence Exploit"](https://www.psdevwiki.com/ps2/PS2_Independence)

## IOP Side
`TBIN` is the PSX BIOS driver stored in the PS2 BIOS. It lacks the shell and jumps directly to the PS logo when loading a game. Unknown what other differences exist compared to real PSX models.  
`RESET` will jump to `TBIN` if `COP0.PRid` is less than `0Fh` or if I/O register `1F801570.3` is set. Unknown which one is true after the PS2->PSX transition, or if both are true after that.  
Unknown what `TBIN` means. A string in it says `PS compatible mode by M.T.`, which most likely refers to a person. If that is the case, `TBIN` could mean T's Binary, whoever "T" is.

The most significant change when transitioning to PSX mode is that the IOP is underclocked to _33.8688 MHz_, the original speed of the PSX. SPU2 also outputs samples at _44.1 KHz_, rather than _48 KHz_.

# Hacks
The overhead of translating GPU commands, combined with the GS being far, far faster than the PSX GPU, means that the emulator will have different timings from a real PSX. Some PSX games are exceptionally sensitive to timings, so the emulator is able to apply various hacks. All hacks are listed below. Note that the hacks aren't actually given names, so they are made up here.  
- Render Polygon Delay
- Render Rectangle Delay
- Force CDROM speed: When greater than `2`, this overwrites the speed setting set in `OSDSYS`.
- GP0 Increased Sync: Makes the emulator spend more time polling the GP0 FIFO directly, allowing it to respond faster to incoming data.
- VRAM Read Delay
- VBLANK Delay: Adds extra scanlines to emulated frame time, making emulated VBLANK interrupts take more time to occur.
- Timer Delay: Makes the emulator not do anything until timer 0's counter is equal to or greater than this value plus 4.
- Misc. Render Hack
  + Bit 0: Strange hack. Ignores VRAM->VRAM transfers if the command word is not 80000000h.
  + Bit 1: When set, makes the render polygon delay only apply if the polygon is textured.