# Subsystem Interface (SIF)

The SIF is how the EE and IOP communicate with each other.
SIF has some mailbox hardware registers that the CPUs can use to pass values to each other, which happens during SIF initialization.  
However, once both sides have booted, they use the SIF0 (IOP->EE) and SIF1 (EE->IOP) DMA channels to communicate.

{% include list.liquid all=true %}
