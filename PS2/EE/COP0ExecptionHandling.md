---
sort: 6
---

# EE COP0 Exception Handling

## Exceptions
> An exception is an exceptional event that stops the CPU's current task and forces it to address the event, usually with kernel privileges.
On the EE, there are exceptions for interrupts, TLB management, syscalls, and various errors. When an exception occurs, the EE jumps to one of the following vectors:


Name | Normal | Bootstrap | Level  |
---- | ------ | --------- | ------ |
| Reset/NMI         | BFC00000h | BFC00000h | 2 |
| TLB Refill        | 80000000h | BFC00200h | 1 |
| Perf. Counter     | 80000080h | BFC00280h | 2 |
| Debug             | 80000100h | BFC00300h | 2 |
| All others        | 80000180h | BFC00380h | 1 |
| Interrupt         | 80000200h | BFC00400h | 1 |

The full exception handling works as follows.
```c
  void handle_exception_level1(u32 vector, u8 cause)
  {
      Cause.ExcCode = cause;
      if (in_branch_delay)
      {
          EPC = PC - 4;
          Cause.BD = true;
      }
      else
      {
          EPC = PC;
          Cause.BD = false;
      }
      Status.EXL = true;
      PC = vector;
  }
  
  void handle_exception_level2(u32 vector, u8 cause)
  {
      Cause.ExcCode = cause;
      if (in_branch_delay)
      {
          ErrorEPC = PC - 4;
          Cause.BD2 = true;
      }
      else
      {
          ErrorEPC = PC;
          Cause.BD2 = false;
      }
      Status.ERL = true;
      PC = vector;
  }
```

### `$08` - COP0.BadVAddr
0-31 Virtual address that caused an exception BadVAddr is written to when a TLB Refill, TLB Modified, TLB Invalid, or Address Error exception occurs.

### `$12` - COP0.Status
- 0: IE - Interrupt enable
- 1: EXL - Exception level (set when a level 1 exception occurs)
- 2: ERL - Error level (set when a level 2 exception occurs)
- 3-4:   KSU - Privilege level
  + 0:Kernel
  + 1:Supervisor
  + 2:User
- 10:    INT0 enable (INTC)
- 11:    INT1 enable (DMAC)
- 12:    Bus error mask - when set, bus errors are disabled
- 15:    INT5 enable (COP0 timer)
- 16:    EIE - Master interrupt enable
- 17:    EDI - If not set, EI/DI only works in kernel mode
- 18:    CH - Status of most recent Cache Hit instruction
  + 0: miss
  + 1: hit
- 22    BEV - If set, level 1 exceptions go to "bootstrap" vectors in BFC00xxx
- 23    DEV - If set, level 2 exceptions go to "bootstrap" vectors in BFC00xxx
- 28-31 Usability of coprocessors 0-3
  + If not set, using a coprocessor raises an exception

<div class="flash flash-full">
  
All interrupts are disabled unless the following condition is true.
  
```
Status.IE && Status.EIE && !Status.EXL && !Status.ERL
```

When `Status.ERL` or `Status.EXL` is set, the EE operates in kernel mode regardless of `Status.KSU`.
  

</div>


### $13 - COP0.Cause
- 2-6   Exception code
  + 00h:Interrupt
  + 01h:TLB Modified
  + 02h:TLB Refill (instruction fetch or load)
  + 03h:TLB Refill (store)
  + 04h:Address Error (instruction fetch or load)
  + 05h:Address Error (store)
  + 06h:Bus Error (instruction)
  + 07h:Bus Error (data)
  + 08h:Syscall
  + 09h:Breakpoint
  + 0Ah:Reserved Instruction
  + 0Bh:Coprocessor Unusable
  + 0Ch:Overflow
  + 0Dh:Trap
- 10    INTC interrupt pending
- 11    DMAC interrupt pending
- 15    COP0 timer interrupt pending
- 16-18 Error code
  + 00h=Reset
  + 01h=NMI
  + 02h=Performance counter
  + 03h=Debug
- 28-29 Coprocessor that triggered a CU exception
- 30    BD2 - Set when a level 2 exception occurs in a delay slot
- 31    BD - Set when a level 1 exception occurs in a delay slot

Cause is used to determine what caused an exception.

The BD/BD2 bits are important as when they are set, EPC/ErrorEPC is set to the address of the branch instruction, not its delay slot. This allows the EE to re-execute the branch when it returns from an exception.

### $14/$30 - COP0.EPC/COP0.ErrorEPC
- 0-31  Address to return to after an exception
EPC and ErrorEPC are used by level 1 and 2 exceptions respectively. When the ERET instruction is executed, it jumps to the address in EPC or ErrorEPC, depending on if Status.EXL or Status.ERL is set.

### $23 - COP0.BadPAddr
- 0-31  Physical address that caused an exception
BadPAddr is written to when a bus error occurs and Status.BEM is not set.
