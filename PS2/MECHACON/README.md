# MECHACON

The **MECHA**nics **CON**troller, the chip in control of the drive mechanism. However, this chip also handles security in the PlayStation 2 and implements game disc security, Magic Gate and KELF file decryption among other things.

he earlier one is based on the 16-bit SPC970 CPU core, running at 33.8688 Mhz, and was used on G-chassis and eariler boards. Chip name starts with "CXP10". D/D'-chassis and earlier boards come with a 100-pin QFP package. F/G-chassis boards come with a 136-ball BGA package.

The newer one is ARM-based (Sony SR11 core, 32-bit, ARM7TDMI, running ARM v4t little endian using both 32-bit ARM and 16-bit Thumb code), codenamed "Dragon", and used from H-chassis onwards. Chip name starts with "CXR7". Comes in a 164-ball BGA package. RAM installed is 16384 bytes/16KiB, but firmware (checked on 5.12) can use up to 13024 bytes of memory.

The "Dragon" variant also fulfills the functions that were undertaken by the separate SysCon chip on G-chassis and earlier boards, as well as the Rohm EEPROM+RTC chip, which was separate on G-chassis and earlier boards (or dedicated RTC and EEPROM chips on early F-chassis and earlier boards).

> thanks to [psdevwiki](https://www.psdevwiki.com/ps2/MechaCon) for this brief introduction

> thanks to kr_ps2 for the tables of commands