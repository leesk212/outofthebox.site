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
* 슈퍼 스칼라와 같이 CPU 내에 파이프 라인에 4개에서~5개 되는 명령어들이 같이 패치 되게된다.  
  * 그리고 그 명령어들은 마이크로옵스에 의해 디코딩 되어지고 Reorder Buffer로 복사가 되어진다.
* 이때 Reorder Buffer에서 의존성이 있는 레지스터들을 renaming 해주는 등의 과정을 진행해주어 in-order에서 out-of-order로 최적화된 명령어 수행이 된다. 그리고 queue(Unified Reservation Station)에 들어가게 될때 원래의 순서에 맞게 정렬이 되어진다.

### Speculative excution
* 이는 out-of-order와 다른 excution이다. 
> IF( CONDITION ){ } ELSE { }
에서, 추측실행이란 CONDITION이 맞다는 가정하에 CONDITION안의 BODY를 실행시키는 명령임이다.
* Out of order excution과 구분이 필요하다.

## Cache Attacks

# A Toy Example
> raise_exception();  
> access(probe_array[data * 4096])  

* 이 과정에서 집중해야하는 것은 data의 * 4096번지를 곱한 것이다. 그 이유는 flush+reload에서 아이디어를 얻을 수 있는 것과 마찬가지로, prefetch때문이다. flush+reload에서 로드하는 과정에서 페이지 단위로 로드를 하기 때문에 정확히 어느 곳의 접근을 했는지에 대해서 파악할 수 없다. 그렇기에 그것을 막아주고자 data에 4096을 곱하여 원하는 커널 주소(데이터)의 값을 파악하기 용이하게 만들 수 있다. 
* 첫번째 라인에서 원래대로라면 EXCEPTION이 발동되어, 두번째 명령어가 실행이 되면 안된다. 
* 하지만 슈퍼스칼라에 의해서 두가지의 명령어가 같이 페치되어, Reorder Buffer에서 두개의 명령어다 실행이 가능한 상태로 메모리 접근을 한다.
* 그렇게 되면 이미 접근한 메모리에 대한 페이지들이 캐시에 올라와있게 되고, 실행된 probe_array의 모든 페이지들을 Flush+Reload를 진행하게되면 data에 특정 페이지에 대한 접근만 빠르게 됨으로 어느 페이지가 물리 메모리에 접근 되었는지를 확인할 수 있게된다. 
* 

