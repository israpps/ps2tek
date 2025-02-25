---
sort: 6
---
# BIOS EE Syscalls

When a SYSCALL instruction is executed, the EE jumps to `80000180h` and sets `COP0.Cause` to `08h`. The kernel syscall handler retrieves the syscall number from the `v1` register.  
Negative syscalls use the current thread's stack for internal operations and are meant to be used in interrupt handlers. Positive syscalls use the kernel stack, and some of them can trigger thread reschedules.

## List of EE Syscalls
### `01h`: `void ResetEE(int reset_flag)`
Resets EE components depending on which bits are set in reset_flag:
- 0 - DMAC
- 1 - VU1
- 2 - VIF1
- 3 - GIF
- 4 - VU0
- 5 - VIF0
- 6 - IPU

```note
INTC is reset regardless of the flag's settings.
```

### `02h`: `void SetGsCrt(bool interlaced, int display_mode, bool frame)`
Initializes the PCRTC.

### `04h`: `void Exit(int status)`
Returns to the OSDSYS browser. Internally calls `LoadExecPS2("rom0:OSDSYS", 1, "BootBrowser")`.

### `05h`: `void _ExceptionEpilogue/RFU005()`
Internal syscall used by kernel exception handlers. Returns to the user program.

### `06h`: `void LoadExecPS2(const char* filename, int argc, char** argv)`
Loads an ELF with the specified arguments and executes it. This function also clears all of the internal kernel state, such as threads and semaphores, before execution.  
This function does not (and must not) return.

### `07h`: `void ExecPS2(void* entry, void* gp, int argc, char** argv)`
Clears all of the internal kernel state and creates a thread with priority 0 (main thread). The thread begins executing from the entry point.  
This function does not (and must not) return.

### `10h`: `int AddIntcHandler(int int_cause, int (*handler)(int), int next, void* arg, int flag)`
Adds an interrupt handler with the specified cause to a queue. This function returns the id of the handler or `-1` if the operation fails. `int_cause` corresponds to a bit in `INTC_STAT`/`INTC_MASK`. For example, `int_cause=2` will register a VBLANK handler.  
next is a previously registered handler id which this handler will be placed before. `next==0` will place the handler in front of the queue, and `next==-1` will place it at the back of the queue.  
`arg` and `flag` are stored in the handler struct but don't seem to be used otherwise?

### `11h`: `int RemoveIntcHandler(int int_cause, int handler_id)`
Removes the handler associated with the interrupt cause. Returns `-1` if the operation fails.

### `12h`: `int AddDmacHandler(int dma_cause, int (*handler)(int), int next, void* arg, int flag)`
Adds an interrupt handler for `INT1` (DMAC) interrupts. Otherwise works the same as `AddIntcHandler`.

### `13h`: `int RemoveDmacHandler(int dma_cause, int handler_id)`
Works same as `RemoveIntcHandler`.

### `14h`: `bool _EnableIntc(int cause_bit)`
Enables the indicated bit in `INTC_MASK`. Returns true if this bit was set to `0` and false if it was already set to `1`.

### `15h`: `bool _DisableIntc(int cause_bit)`
Disables the indicated bit in `INTC_MASK`. Returns true if this bit was set to `1` and false if it was already set to `0`.

### `16h`: `bool _EnableDmac(int cause_bit)`
Same as `_EnableIntc`, but for `D_STAT`'s mask.

### `17h`: `bool _DisableDmac(int cause_bit)`
Same as `_DisableIntc`, but for `D_STAT`'s mask.

### `20h`: `int CreateThread(ThreadParam* t)`
Creates a thread with `DORMANT` status. The thread is not placed in the priority linked-list.
Only `t->func`, `t->initial_priority`, `t->stack`, `t->stack_size`, and` t->gp_reg` are used. The new thread re-uses the heap of its parent thread.
Returns the id of the newly created thread, or `-1` if the function fails.

### `21h`: `void DeleteThread(int thread_id)`
Deletes a thread. The function only succeeds if the thread has DORMANT status and is not the current thread.

### `22h`: `void StartThread(int thread_id, void* arg)`
Adds a `DORMANT` thread to the active thread list and gives it `READY` status, forcing a thread reschedule. arg is intended to be used by the callee thread if it becomes the current thread.
The function fails if the thread is the current thread or is not `DORMANT`.

### `23h`: `void ExitThread()`
Removes the current thread from the active thread list, resets it, and gives it `DORMANT` status, forcing a thread reschedule.

### `24h`: `void ExitDeleteThread()`
Deletes the current thread, forcing a thread reschedule.

### `25h`: `void TerminateThread(int thread_id)`
Has different behavior depending on the thread's status:
- No status (0), `RUN`, `DORMANT`: No effect.
- `READY`: Removes thread from active thread list, resets it, and gives it `DORMANT` status, forcing a thread reschedule.
- `WAIT`, `WAITSUSPEND`: Same as READY but also decrements the thread's semaphore's "wait_threads" by one.
- All other statuses: Resets the thread and gives the thread `DORMANT` status, forcing a thread reschedule.

### `26h`: `void iTerminateThread(int thread_id)`
Same as `TerminateThread`, but does not force a thread reschedule. Used internally by `TerminateThread`.  
Returns `-1` if unsuccessful.

### `29h`: `int ChangeThreadPriority(int thread_id, int priority)`
Changes the thread's priority, forcing a thread reschedule. If `thread_id==0`, the current thread's id is used.  
This function fails if the thread is `DORMANT`.

### `2Ah`: `int iChangeThreadPriority(int thread_id, int priority)`
Same as `ChangeThreadPriority`, but does not force a thread reschedule. Used internally by `RotateThreadReadyQueue`.  
Returns `-1` upon a failure, or the thread's old priority on a success.

### `2Bh`: `void RotateThreadReadyQueue(int priority)`
Rotates the threads in the given priority's linked list, forcing a thread reschedule. This allows different threads of the same priority level to execute.

### `2Ch`: `int _iRotateThreadReadyQueue(int priority)`
Same as `RotateThreadReadyQueue`, but does not force a thread reschedule. Used internally by `ChangeThreadPriority`.  
Returns `-1` upon a failure, or the given priority on a success.

### `2Dh`: `void ReleaseWaitThread(int thread_id)`
If the thread has `WAIT` status, it is given `READY` status and added to the active thread list. If it has `WAITSUSPEND` status, it is given `SUSPEND` status.  
Both (and only) these cases force a thread reschedule. The reference to the thread's semaphore is also removed.

### `2Eh`: `int iReleaseWaitThread(int thread_id)`
Same as ReleaseWaitThread, but does not force a thread reschedule. Internally used by ReleaseWaitThread.
Returns -1 if unsuccessful.

### `2Fh`: `int GetThreadId()`
Returns the current thread's id.

### `30h`: `int ReferThreadStatus(int thread_id, ThreadParam* status)`
### `31h`: `int iReferThreadStatus(int thread_id, ThreadParam* status)`
Fills out the ThreadParam struct with the given thread's information. Returns `-1` if unsuccessful.
The "i" variant performs exactly the same function.

### `32h`: `void SleepThread()`
If the current thread's "wakeup_count" is greater than zero, it is decremented. Else, if the thread status is `RUN` or `READY`, the current thread is removed from the active thread list and set to `WAIT`, forcing a thread reschedule.

### `33h`: `void WakeupThread(int thread_id)`
Has different behavior depending on the thread's status:
- `WAIT` (sleeping): Set to READY and re-added to active thread list, forcing a thread reschedule.
- `WAITSUSPEND` (sleeping): Placed in SUSPEND status, forcing a thread reschedule.
- `READY`, `SUSPEND`, `WAIT/WAITSUSPEND` (semaphore): Increments "wakeup_count", forcing a thread reschedule.
- Other statuses: No effect.

### `34h`: `int iWakeupThread(int thread_id)`
Same as `WakeupThread`, but does not force a thread reschedule. Internally used by `WakeupThread`.
Returns `-1` if unsuccessful.

### `35h`: `int CancelWakeupThread(int thread_id)`
### `36h`: `int iCancelWakeupThread(int thread_id)`
Resets the thread's "wakeup_count" to zero. Returns `-1` if unsuccessful, or the old wakeup_count otherwise.

### `37h`: `int SuspendThread(int thread_id)`
### `38h`: `int iSuspendThread(int thread_id)`
Has different behavior depending on the thread's status:
- `READY`, `RUN`: Removed from active thread list and placed in SUSPEND status.
- `WAIT`: Placed in WAITSUSPEND status.
- All other statuses: No effect.

Returns `-1` on failure or thread_id otherwise. 

```warning
BUG: This function does NOT force a thread reschedule! If the current thread is suspended, it will continue to run!
```

### `39h`: `void ResumeThread(int thread_id)`
Has different behavior depending on the thread's status:
- `SUSPEND`: Placed in READY or RUN status and added to active thread list, forcing a thread reschedule.
- `WAITSUSPEND`: Placed in WAIT status, forcing a thread reschedule.
- All other statuses: No effect

### `3Ah`: `int iResumeThread(int thread_id)`
Same as `ResumeThread`, but does not force a thread reschedule. Internally used by `ResumeThread`.
Returns `-1` if unsuccessful or thread_id otherwise.

### `3Bh`: `void JoinThread()`

### `3Ch`: `void* InitMainThread/RFU060(uint32 gp, void* stack, int stack_size, char* args, int root)`
Initializes the current thread. Returns the stack pointer of the thread.  
If `stack == -1`, the stack pointer equals the end of RDRAM - stack_size. Else, it equals stack + stack_size.  
This function should only be called before the program's main function.

### `3Dh`: `void* InitHeap/RFU061(void* heap, int heap_size)`
Initializes the current thread's heap. If `heap == -1`, the end of the heap resides at the thread's stack pointer. Else, the end of the heap is heap + heap_size.  
Returns the end of the thread's heap.

### `3Eh`: `void* EndOfHeap()`
Returns the current thread's heap base.

### `40h`: `int CreateSema(SemaParam* s)`
Creates a semaphore. Returns the semaphore's id if successful and `-1` if not.  
Only `s->init_count` and `s->max_count` need to be specified. `s->attr` and `s->option` may also be specified.

### `41h`: `int DeleteSema(int sema_id)`
Deletes the semaphore, forcing a thread reschedule. Threads waiting on the semaphore will either be released or suspended, depending on their status.

### `42h`: `int SignalSema(int sema_id)`
Signals a semaphore. If a thread has called WaitSema on this semaphore, this forces a thread rescheduling. Otherwise, the semaphore's count is incremented by one.  
Returns `-1` if unsuccessful.

### `43h`: `int iSignalSema(int sema_id)`
Similar to `SignalSema`, except this does not reschedule threads. The semaphore's threads are simply re-added to the active thread list.  
This function is called internally by SignalSema. Returns `-1` if unsuccessful and `-2` if the thread is released from its wait state.

### `44h`: `void WaitSema(int sema_id)`
If the semaphore's "count" variable > 0, count is decremented. Else, a thread rescheduling occurs, changing the active thread. The caller thread's status is set to WAIT.

### `45h`: `int PollSema(int sema_id)`
### `46h`: `int iPollSema(int sema_id)`
Decrements the semaphore's count variable. Returns `-1` if unsuccessful.

### `64h`: `void FlushCache(int mode)`
  Modes of operation
- mode=0: Flush data cache (invalidate+writeback dirty contents to memory)
- mode=1: Invalidate data cache
- mode=2: Invalidate instruction cache

All other modes invalidate both caches.

### `70h`: `uint64_t GsGetIMR()`
Returns the value of the privileged register `GS_IMR`.

### `71h`: `void GsPutIMR(uint64_t value)`
Sets `GS_IMR` to the value.

### `73h`: `void SetVSyncFlag(int* vsync_occurred, u64* csr_stat_on_vsync)`
Sets two kernel pointers that are used in the default INTC interrupt handler when a VSYNC interrupt has occurred.  
If vsync_occurred is not **NULL**, the handler sets the value pointed to by vsync_occurred to `1`.  
If both pointers are not **NULL**, the value pointed to by csr_stat_on_vsync is set to `GS_CSR`.  

A good use of this syscall is to retrieve the even/odd interlacing field in `GS_CSR` as early as possible.

### `74h`: `void SetSyscall/RFU116(int index, int address)`
Replaces an entry on the syscall table with the specified address.  
This function does not perform any bounds checking.

### `76h`: `int SifDmaStat(unsigned int dma_id)`
Returns a positive value if the SIF transfer is queued, `0` if the transfer is in progress, and a negative value if the transfer has completed.

### `77h`: `unsigned int SifSetDma(SifDmaTransfer* trans, int len)`
Low-level syscall for starting SIF1 transfers. The format of SifDmaTransfer is as follows.

```c
struct SifDmaTransfer //Internal semaphore structure
{
  void* src; //EE source
  void* dest; //IOP destination
  int size; //Size in bytes
  int attr;
}
```

The len parameter is how large the `SifDmaTransfer` array is. Multiple SIF1 transfers are queued by the kernel.  
This function returns the ID of the current transfer. It does not wait for the transfer to complete.

### `78h`: `void SifSetDChain()`
Initializes the SIF0 channel by resetting QWC and setting CHCR to `184h` (channel busy, chain mode, TTE bit in tag enabled).

### `7Bh`: `void ExecOSD(int argc, char** argv)`
Shorthand for `LoadExecPS2("rom0:OSDSYS", argc, argv)`.

### `7Dh`: `void PSMode()`

### `7Eh`: `int MachineType()`
Unknown purpose or actual utility.
- Retail and Arcade Hardware return `0`
- PSX DESR returns `1`

```note
this COULD be related to the MachineType Field on the KELFs header
```

### `7Fh`: `int GetMemorySize()`
Returns the amount of RDRAM on the console in bytes.

### `0x80`: `void _GetGsDxDyOffset(int mode, int *dx, int *dy, int *dw, int *dh)`
Internal function for getting board-specific offsets, only present in later kernels (ROMVER > `20010608`).

### `0x82`: `int _InitTLB()`

### `0x83`: `FindAddress`

### `0x85`: `SetMemoryMode(int mode)`
Sets the memory size.

```warning
This syscall only works on PSX DESR

Namco system 256/148 and DTL-T models dont support this syscall although they also have more than 32MB of ram
```
- `mode == 1`: 32MB mode
- `mode != 1`: 64MB mode

The mode is only binding when either `_InitTLB()` or the PSX `ExecPS2()` syscall is called.
Also, a TLB exception will trigger if the Stack Pointer resides beyond

### `0x86`: `int GetMemoryMode()`
Gets the value set by `0x85` syscall, only works on PSX DESR

### `0x87`: `ExecPSX`
Only works on PSX DESR
