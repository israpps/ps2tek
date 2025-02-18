---
sort: 7
---

# BIOS EE Patches

The EE kernel is full of bugs, so games have to work around them. Both the official SDK and PS2SDK patch the alarm functions and certain threading functions before the main function is executed.

## Alarm Patches
Using `SetSyscall`, games first set syscall `5Ah` to be a memcpy with kernel privileges. `5Ah` is then used to copy the real patch and EENULL to `80076000h` and `00082000h` respectively.
The patch contains syscalls `FCh`-`FFh`, which are `SetAlarm`, `iSetAlarm`, `ReleaseAlarm`, and `iReleaseAlarm` respectively. It also patches the interrupt handler for timer 3 (`INTC12`) by setting "syscall" `12Ch`, which is out of bounds.

## Threading Patches
`iWakeupThread` and `iSuspendThread` do not work properly when used on the currently executing thread, or its priority in the case of `iRotateThreadReadyQueue`.
While not technically a patch, games resolve this by creating a "top thread" whose purpose is to redirect these interrupt syscalls to their non-interrupt variants, which forces thread reschedules.
