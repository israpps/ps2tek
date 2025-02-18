---
sort: 4
---

# BIOS EE Threading

The EE kernel uses a cooperative priority-based thread scheduler. It can support up to 256 threads and 256 semaphores. 128 priority levels are available.

## Structs
```c
struct ThreadParam //Used as argument for CreateThread, ReferThreadStatus
{
  int status;
  void *func; //function to execute when thread begins
  void *stack;
  int stack_size;
  void *gp_reg;
  int initial_priority;
  int current_priority;
  u32 attr;
  u32 option;
}
struct SemaParam //Used as argument for CreateSema
{
  int count, //used by WaitSema and SignalSema	      max_count,	      init_count, //initial value for count	      wait_threads; //number of threads associated with this semaphore	  u32 attr, //not used by kernel	      option; //not used by kernel
}
//Thread statuses
#define THS_RUN 0x01
#define THS_READY 0x02
#define THS_WAIT 0x04
#define THS_SUSPEND 0x08
#define THS_WAITSUSPEND 0x0C //THS_WAIT | THS_SUSPEND
#define THS_DORMANT 0x10
struct TCB //Internal thread structure
{
  struct TCB *prev;
  struct TCB *next;
  int status;
  void *func;
  void *current_stack;
  void *gp_reg;
  short current_priority;
  short init_priority;
  int wait_type; //0=not waiting, 1=sleeping, 2=waiting on semaphore
  int sema_id;
  int wakeup_count;
  int attr;
  int option;
  void *_func; //???
  int argc;
  char **argv;
  void *initial_stack;
  int stack_size;
  int *root; //function to return to when exiting thread?
  void *heap_base;
}
struct thread_context //Stack context layout
{
  u32 sa_reg;  // Shift amount register
  u32 fcr_reg;  // FCR[fs] (fp control register)
  u32 unkn;
  u32 unused;
  u128 at, v0, v1, a0, a1, a2, a3;
  u128 t0, t1, t2, t3, t4, t5, t6, t7;
  u128 s0, s1, s2, s3, s4, s5, s6, s7, t8, t9;
  u64 hi0, hi1, lo0, lo1;
  u128 gp, sp, fp, ra;
  u32 fp_regs[32];
}
struct sema //Internal semaphore structure
{
  struct sema *free; //pointer to empty slot for a new semaphore
  int count;
  int max_count;
  int attr;
  int option;
  int wait_threads;
  struct TCB *wait_next, *wait_prev;
}
```

## Thread Scheduler
The kernel uses an array of 128 doubly-linked lists for managing thread priorities. The general algorithm for the scheduler is as follows:

```
  void reschedule(uint32 EPC, uint32 stack)
    Set current thread entry function to EPC (instruction after exception)
    Set current thread stack base to stack
    Set current thread status to READY
    Loop through active thread priority list, starting from 0 (highest priority)
      If an active thread is found, set current thread to it and break
      If no active thread is found, print an error and call Exit(1)
    Set found thread's status to RUN and return its entry function and stack pointer
```

Scheduling is only invoked by syscalls and relies on absolute priority. Threads with lower priority will never run as long as there is an active thread with higher priority.  
When the BIOS receives an interrupt, it will save the GPR registers into a buffer, so that the interrupt can be served without destroying the thread state. However, when a thread is paused (ie. yields the CPU) its CPU context is placed on the user stack using the context described above.

## Semaphores
Threads that call WaitSema on a semaphore with a "count" of zero are placed in a WAIT state and removed from the active thread list.  
Conversely, calling SignalSema re-adds a thread waiting on a semaphore to the active list.  
Semaphores are useful for blocking execution of a thread until a task completes. Sony's official SIF protocol, for instance, use a semaphore to place the caller thread to sleep while the IOP processes a request. The caller thread is reactivated in the SIF interrupt handler.
