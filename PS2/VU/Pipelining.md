---
sort: 4
---

# VU Pipelining

Underneath the surface, the VU is full of quirks. This is partially because its pipeline is exposed to the program.
## FMAC Pipeline

```
-------------
|M|T|X|Y|Z|S|
  |M|T|X|Y|Z|
    |M|T|X|Y|
      |M|T|X|
-------------
```

The FMAC pipeline is split into six stages:

M: Read instruction from memory

T: Read VU registers

X/Y/Z: Execute stages.

Calculation/flag writeback.

In ideal conditions, one FMAC instruction can be executed every cycle. However, read-after-write hazards - reading a register before its write has completed - induces 
an FMAC stall for up to three cycles, allowing the previous write to complete.

The FMAC pipeline is used for all instructions except DIV, SQRT, RSQRT, integer calculation, and branching. Integer load/stores also use this pipeline and are subject 
to hazard checks.

Hazard checks are NOT performed for ACC, VF00, I, Q, P, and R. Hazard checks are performed on each VF field, so writing to VF01y and reading from VF01x does not induce 
a stall.

### Integer Pipeline
```
-------------
|M|T|X|Y|Z|S|
  |M|T|X|Y|Z|
    |M|T|X|Y|
      |M|T|X|
-------------
```

The integer pipeline is almost the same as the FMAC pipeline, except that all integer instructions have a latency of 1 cycle due to pipeline bypassing.
The other execute and writeback stages are dummy stages to align with the FMAC pipeline.

### FDIV Pipeline
#### DIV/SQRT
```
-------------------------
|M|T|D1|D2|D3|D4|D5|D6|F|
-------------------------
```

#### RSQRT
```
-------------------------
|M|T|D1| ........ |D12|F|
-------------------------
```

The FDIV pipeline is used by DIV, SQRT, and RSQRT. It is similar to the FMAC pipeline, though it has more execution stages.

It can execute concurrently with the other pipelines. However, only one FDIV instruction can execute at a time, and trying to execute another FDIV instruction before 
the first has completed will induce a stall that affects the whole VU. The WAITQ instruction also induces a stall and can be used for synchronization.

Since no hazard checks are performed when reading Q, when Q is read in the middle of an FDIV instruction, the old value of Q is retrieved.

DIV and SQRT have a latency of 7 cycles, and RSQRT has a latency of 13 cycles. The maximum stall time for DIV/SQRT and RSQRT is 6 and 12 cycles, respectively.

### EFU Pipeline
```
-------------------------
|M|T|N1|N2| ...... |Nn|P|
-------------------------
```

The EFU pipeline is used by elementary function instructions exclusive to VU1. It is almost the same as the FDIV pipeline, with the only difference being that hazard 
checks do not occur for the writeback stage, meaning that a stall lasts for one cycle less than usual.

The list of latencies for the EFU instructions is as follows.


|  .                            |  . |
| ----------------------------- | -- |
| EATAN/EATANxy/EATANxz         | 54 |
| EEXP                          | 44 |
| ELENG                         | 18 |
| ERCPR                         | 12 |
| ERLENG                        | 24 |
| ERSADD                        | 18 |
| ERSQRT                        | 18 |
| ESADD                         | 11 |
| ESIN                          | 29 |
| ESQRT                         | 12 |
| ESUM                          | 12 |


The WAITP instruction, like WAITQ, induces a stall on the EFU pipeline.

## XGKICK Pipeline
XGKICK uses the FMAC pipeline, but the PATH1 transfer it starts occurs concurrently with VU operation. However, if a second XGKICK is executed in the middle of the first, the instruction after the second XGKICK will stall until the PATH1 transfer is complete.
