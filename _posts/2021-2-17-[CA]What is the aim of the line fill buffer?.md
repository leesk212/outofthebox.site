---
tags: computer-architecture
toc: True
---
# Q. What is the aim of the line fill buffer?

 
From the Intel Software Optimization Manual and different discussion on this forum, I discovered there is a buffer from the L1 data cache and the L2 called Line Fill Buffer(LFB). 

What is the purpose of such buffer? why not transferring directly data from L2 to L1.

I first believed that it is because the bus between a L1 and L2 cannot have the same size than a cache line size due to electronic constraints as explained by https://stackoverflow.com/questions/39182060/why-isnt-there-a-data-bus-which-is-as-wide-as-the-cache... but I now believe that it has nothing to do with that (the same constraints should also apply between the L1d and the LFB).

I have observed this buffer between L1d and L2 but I do not understand why there is no such buffer between the L1 instruction cache and L2. 

This is perhaps due to the fact that cache instruction is not written by the processor(no data transfer from CPU registers to instruction cache for instance)?

Also, I am wondering if there is a relation between cache coherency protocol and the LFB according to you?'

# A

The Line Fill Buffer between the L1 Data Cache and the L2 Unified Cache exists to track outstanding L1 Data Cache misses and to interface between the byte-oriented loads and stores in the core and the cache-line transfers outside of the L1 Data Cache.

The Line Fill Buffer tracks L1 Data Cache misses at a cache line level, so that multiple load misses or store misses to the same cache line won't use up all the L1 Data Cache outstanding miss tracking buffers. 

A Line Fill Buffer (or what AMD calls a "Miss Address Buffer") certainly has functionality involving the addresses of loads and stores that miss in the cache, but may also have functionality involving the data moving in and out of the L1 Data Cache.

The function(s) of the LFB and its relationship to other components and functionality of the system have been discussed frequently in these (and other) forums. 

## Examples include:

"How does WC-buffer relate to LFB?"  https://software.intel.com/en-us/forums/intel-moderncode-for-parallel-architectures/topic/843581

"Single Threaded Memory Bandwidth on Sandy Bridge"  https://software.intel.com/en-us/forums/software-tuning-performance-optimization-platform-monitoring/topic/480004

"out-of-order (OOO) microarchitecture"  https://software.intel.com/en-us/forums/software-tuning-performance-optimization-platform-monitoring/topic/595220

## Reference
https://community.intel.com/t5/Intel-Moderncode-for-Parallel/What-is-the-aim-of-the-line-fill-buffer/td-p/1180777
