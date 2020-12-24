# Abstract

# Introduction

# Background

## Out of oreder excution
* In practice, CPUs supporting out-of-order execution allow running operations **speculatively** to the extent that the processor's out-of-order logic processess instructions before the CPU is certain that the instruction will be needed and committed.
* In this paper, we refer to speculative execution in a more restricted meaning, where it refers to an instruction sequence following a branch, and use the term out of order execution to refer to any way of getting an operation executed before the processor has committed the results of all prior instructions.
* For dynamic scheduling of instructions to allow out-of-order execution
  * A unified reservation station allows **a CPU to use a data value** as it has been *computed* instead of *sorting* it in a register and *re-reading* it.
  * The reservation station renames registers to allow instructions that operate on the same physical registers to use the last logical one to solve read-after-write, WAR, WAW hazards 
  > MULTD F4,F2,F2  
  > ADDD F2,F0,F6(X)  
  > ADDD F8,F0,F6(Dependency on F2 don't occur)
