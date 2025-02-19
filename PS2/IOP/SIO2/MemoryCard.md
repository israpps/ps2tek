---
sort: 2
---

# SIO2 PS2 Memcards

## Basic Info

A PS2 memory card uses flash memory. The main advantages are that it is cheap and does not require any power to retain information. The downsides are that accessing flash is much slower than RAM, and flash memory will go bad when written to too many times.

Flash also has some restrictions on how it can be written to. Depending on the card, individual bits can be changed from 0 to 1 or 1 to 0, but not both. In order to flip a bit in the opposite direction, the entire block must be erased.

Official memory cards are 8 MB, but third-party cards can be larger than this. Certain games do not work if the memory card is larger than expected, for unknown reasons.

## Commands
### `11h` - Probe

 .  |  .  |
--- | --- | 
Command| 11 + 1 XX byte
Reply| 2B + Terminator
Length| 2 bytes

First command sent when trying to detect a card.

The terminator defaults to `55h` on reset. Newer versions of MCMAN check for this.

### `12h` - Unk12

 .  |  .  |
--- | --- | 
Command| 12 + 1 XX byte
Reply| 2B + Terminator
Length| 2 bytes

Unknown purpose. Sent on write/erase commands. Maybe some sort of flush?

### `21h` - Start Erase
### `22h` - Start Write
### `23h` - Start Read

 .  |  .  |
--- | --- | 
Command| 21 + 4-byte sector address + 2 XX bytes
Reply| 5 XX bytes + 2Bh + Terminator
Length| 7 bytes

These commands prepare read/write/erase sector operations by providing a starting address in sectors.

### `26h` - Get Specs

 .  |  .  |
--- | --- | 
Command| 26 + 10 XX bytes
Reply| 2B + 2-byte sector size in bytes + 2-byte erase block size + 4-byte sector count + 1-byte checksum + Terminator
Length| 11 bytes


Retrieves the card's capabilities. The total size of the memory card is (sector size + 16) * sectors (the 16 is space for error correction).

The checksum is an XOR between the individual bytes of sector size, erase blocks, and sectors.

A standard Sony 8 MB memory card will report the following.

> `2B` `00` `02` `10` `00` `00` `40` `00` `00` `52`

That is to say, a sector size of 512 bytes, an erase block page count of 16 sectors, and a sector count of 16,384.

### `27h` - Set Terminator

 .  |  .  |
--- | --- | 
Command| 27 + 1-byte new terminator + 1 XX byte
Reply| XX + 2B + Old terminator
Length| 3 bytes

### `28h` - Get Terminator

 .  |  .  |
--- | --- | 
Command| 28 + 2 XX bytes
Reply| 2B + Terminator + 55
Length| 3 bytes

### `42h` - Write Data

 .  |  .  |
--- | --- | 
Command| 42 + 1-byte write size + 128 data bytes + 2 XX bytes
Reply| 2B + Terminator + 129 XX bytes + Terminator
Length| 132 bytes

Writes up to 128 bytes at the memory card address given by command `22h`. 128 data bytes must be sent, though anything more than the given size is ignored.

### `43h` - Read Data

 .  |  .  |
--- | --- | 
Command| 42 + 1-byte read size + 130 XX bytes
Reply| 2B + Terminator + 128 data bytes + 1-byte XOR checksum + Terminator
Length| 132 bytes

Reads up to 128 bytes at the address given by command `23h`. The checksum is applied to the amount of bytes read.

### `81h` - Read/Write End

 .  |  .  |
--- | --- | 
Command| 81 + 1 XX byte
Reply| 2B + Terminator
Length| 2 bytes

Sent at the end of a read or write. Unknown purpose.

### `82h` - Erase Block

 .  |  .  |
--- | --- | 
Command| 82 + 1 XX byte
Reply| 2B + Terminator
Length| 2 bytes

Erases data at the address given by command `21h` by setting it all to FFh. On a standard 8 MB card, this would be `(512 + 16) * 16` bytes of data.

### `BFh` - Unknown BF

 .  |  .  |
--- | --- | 
Command| 82 + 2 XX bytes
Reply| 1 XX byte + 2B + Terminator
Length| 3 bytes

Used during card detection.

### `F0h` - AuthXorF0

 .  |  .  |
--- | --- | 
Command| F0 + 1-byte param + variable
Reply| 1 XX byte + 2B + Variable + Terminator
Length| 12 bytes

This strange command is used by SECRMAN while detecting the card and does some XOR checksumming.

it's 1 byte parameter is actually a subcmd:

Sub-CMD |  Purpose |
------- | -------- | 
`0x00` | _Unknown_. called after card auth key change
`0x01` | Requests the card to send CardIV vector
`0x02` | Requests the card to send CardMaterial vector
`0x03` | _Unknown_. called after secrman sent CardIV & CardMaterial to the mechacon
`0x04` | Requests the card to send CardNonce vector
`0x05` | _Unknown_. called after secrman sent CardNonce to the mechacon
`0x06` | sends the MechaChallenge3 data to the card
`0x07` | sends the MechaChallenge2 data to the card
`0x08` | _Unknown_
`0x09` | _Unknown_
`0x0A` | _Unknown_. This command usually fails on magicgate mismatch (eg: retail mechacon and arcade memory card)
`0x0B` | sends the MechaChallenge1 data to the card
`0x0C` | _Unknown_
`0x0D` | _Unknown_
`0x0E` | _Unknown_
`0x0F` | Requests the card to send CardResponse1 data. (wich was generated with the MechaChallenge data)
`0x10` | _Unknown_
`0x11` | Requests the card to send CardResponse2 data
`0x12` | _Unknown_
`0x03` | Requests the card to send CardResponse3 data
`0x14` | _Unknown_. it may signal the end of the secrAuthCard routine. after calling this command. secrauthcard obtains the final result of the mechacon authentication and returns the final value

### `F3h` - AuthF3

 .  |  .  |
--- | --- | 
Command| F3 + 2 XX bytes
Reply| 1 XX byte + 2B + Terminator
Length| 3 bytes

Sent by SECRMAN. Instructs the card to reset cryptography.  
This command is sent at the beginning of SecrAuthCard, also, the failure of certain functions inside secrAuthCard will also result on this command being sent again before the function returns an error.

Some SECRMAN modules with residual printf refer to this as "card auth 60"

### `F7h` - CardAuthKeyChange

 .  |  .  |
--- | --- | 
Command| F7 + 2 XX bytes
Reply| 1 XX byte + 2B + Terminator
Length| 3 bytes

Sent by retail SECRMAN only. named by the Scene as Card Auth Key Change.  
Orders an official retail card to stop using developer magicgate and start using retail magicgate

```tip
For more information on the commands sent by SECRMAN. please refer to the [homebrew SECRMAN Special driver](https://github.com/ps2dev/ps2sdk/tree/master/iop/security/secrman) wich is based on reverse engineering of the SECRMAN Special driver found on the 2.14 DVDPlayer Update discs ([`PBPX_952.22`](http://redump.org/disc/48961/))
```
