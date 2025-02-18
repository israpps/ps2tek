# BIOS IOP REBOOT - SIF Reboot Server

`REBOOT` installs a custom SIF command handler for `80000003h` and waits for this command to be sent.

## IOP Reboot Procedure
The EE sends SIF command `80000003h` with an argument of `rom0:UDNL [last image file]..[first image file] [-v]`. An example image path is `cdrom0:\MODULES\IOPRP243.IMG;1` (sent by Atelier Iris: Eternal Mana).  
The -v flag is recognized by UDNL but doesn't seem to have any effect.  

`REBOOT` calls `MODLOAD.ReBootStart`. `MODLOAD` cleans up all module libaries and jumps to `IOPBOOT`.
`IOPBOOT` bootstraps `SYSMEM` and `LOADCORE`. `LOADCORE` loads all modules in `IOPBTCON2`.
When `MODLOAD` is reloaded, it installs a post-boot callback in `LOADCORE`. This callback jumps to the `UDNL` module.  
`UDNL` scans all provided image files and finds the newest modules in each of them, then triggers yet another reset by bootstrapping `SYSMEM` and `LOADCORE`.
`LOADCORE` reads from `IOPBTCONF` and loads the newest modules in the image files or the ones in the boot ROM. The reboot is now complete.

## Image File Format
Like the BIOS, an image file contains a `ROMDIR` filesystem. An image file usually contains a `RESET` file of zero bytes, `ROMDIR`, `EXTINFO`, and a list of newer IOP modules to load.

An example of the usage for this IOPRP images is the Sony DVDPlayer Update discs (and FreeMcBoot installer as a homebrew example). they use these IOPRP images to replace the SECRMAN driver with an advanced version that allows binding KELFs to a memory card to install them there as system or DVDPlayer Updates