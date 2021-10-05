---
toc: True
tags: 🌟paper-review security-attack 
---
# Abstract
* Intel CPU에만 연구가 집중되어 있다. 그럼으로 비교적 적은 취약점이 다른 CPU에서 발견되었다.
* AMD CPU의 결점을 찾는다, transient execution hijacking 공격을 통해서
* AMD Zen family의 Meltdown/MDS 와 비슷한 패턴을 볼수있다.
* AMD에서의 Meltdown은 Intel CPU와 비교하여서 제한된 취약성을 보이지만, 다른 microarchitectural attack들을 amplify할 가능성이 있을 것이다.

# Core
* For a load instruction issued onto the pipeline to work a TLB hit is required

* For a transient load to work its enough to have only the canonical part of the address to be matched

* If we try to deference the non-canonical addresss when TLB contains an entry with canonical address, 

* the content of canonical address will be passed transiently to the load.

* We suspect that the full address check is done when instruction leaves the pipeline in program order.

* We verified, that source of leakage could be the L1 cache and a not-committed entry from the Store Queue as well.

* AMD Optimization manual describes, that [11:0] bits are used to determine the Store-To-Load-Fowarding(STLF)

* However we did not see any illegal STLF which is triggered by the lowest 12-bit match even within the same address space

* Moreover, to trigger the illegal STLF we noticed, the TLB-hit is not enough.  

* The second condition is store instruciton from the Store Queue has to be an L1DcCache hit.

* We verified our main observation (non-canonical address violation) on both speculative (Spectre-type) and non-speculative (Meltdown-type) execution path.
* To explain this behaviour we learned the patent where it is stated, that AMD CPUs may require the mircro-TLB hit before any load instruction passes Figure 4.
* micro-TLB is dedicated structure, which keeps partial information from the main TLB
* However, we were told by the AMD security team, that the Ryzen-family of CPu is not equipped with micro TLB, but use the main TLB for this check
* In other words, if there is no TLB hit, Load will not be passsed transiently. 
* Hence we found out that the main TLB ignores the upper bits when it compares.
* We also could not trigger this leak between two user spaces running on the same core, obviously because of the TLB flush.
* However, it is possible to "leak" across different threads with the same address space via L1D cache.
