---
toc: True
tags: 🌟paper-review security-attack AMD-Attack csca
---
> ref: https://saidganim.github.io/amdncte.html

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

# Store to load forwarding
* Buffering stores until retirement avoids WAW and WAR dependencies but introduces a new issue. Consider the following scenario: a store executes and buffers its address and data in the store queue. A few instructions later, a load executes that reads from the same memory address to which the store just wrote. If the load reads its data from the memory system, it will read an old value that would have been overwritten by the preceding store. The data obtained by the load will be incorrect.

> Retirement 될때까지 저장을 버퍼링하면 WAW 및 WAR 종속성이 방지되지만, 새로운 issue가 발생한다. 다음의 시나리오를 고려하라: 저장소가 저장소의 대기열의 주소와 데이터를 실행하고 버퍼링한다. 몇가지 명령 후 저장소가 방금 쓴 것과 동일한 메모리 주소에서 읽는 로드가 실행된다. 로드가 메모리 시스템에서 데이터를 읽는 경우 이전 저장소에서 덮어쓴 이전 값을 읽는다. 로드에 의한 데이터는 부정화해질 것이다.

* To solve this problem, processors employ a technique called store-to-load forwarding using the store queue. In addition to buffering stores until retirement, the store queue serves a second purpose: forwarding data from completed but not-yet-retired ("in-flight") stores to later loads. Rather than a simple FIFO queue, the store queue is really a Content-Addressable Memory (CAM) searched using the memory address. When a load executes, it searches the store queue for in-flight stores to the same address that are logically earlier in program order. If a matching store exists, the load obtains its data value from that store instead of the memory system. If there is no matching store, the load accesses the memory system as usual; any preceding, matching stores must have already retired and committed their values. This technique allows loads to obtain correct data if their producer store has completed but not yet retired.

> 이를 해결하기 위해서 프로세서는 저장 대기열을 사용하여 STORE-To-Load-Fowarding이라는 기술을 사용한다. 폐기될 때까지 저장소를 버퍼링하는 것 외에도 저장소 대기열은 완료되지만 아직 폐기 되지 않은 "진행 중" 저장소의 데이터를 이 후 로드로 전달하는 두번째 목적을 제공한다. 단순한 FIFO 대기열이 아니라 저장 대기열은 실제로 메모리 주소를 사용하여 검색된 CAM(Content-Addressable Memory)이다. 로드가 실행되면 프로그램 순서에서 논리적으로 더 빠른 동일한 주소로 이동 중인 스토어에 대해 스토어 큐를 검색한다. 일치하는 저장소가 있는 경우 로드는 메모리 시스템 대신 해당 저장소에서 데이터를 가져온다. 일치하는 저장소가 없으면 로드는 평소같이 메모리 시스템에 엑세스한다. 이전의 일치하는 저장소는 이미 사용 중지되고 값을 커밋해야한다. 이기술을 사용하면 생산자 저장소가 완료되지만 아직 폐기되지 않은 경우 로드에서 올바른 데이터를 얻을 수 있다.

* Multiple stores to the load's memory address may be present in the store queue. To handle this case, the store queue is priority encoded to select the latest store that is logically earlier than the load in program order. The determination of which store is "latest" can be achieved by attaching some sort of timestamp to the instructions as they are fetched and decoded, or alternatively by knowing the relative position (slot) of the load with respect to the oldest and newest stores within the store queue.

> 로드의 메모리 주소에 대한 여러 저장소가 저장소 대기열에 있을 수있다. 논리적으로 더 빠른 최신 스토어를 선택하도록 우선젃으로 인코딩 된다. 어떤 저장소가 최신인지 결정하는 것은 명령이 인출 되고 디코딩 될 때 명령에 일종의 타임스탬프를 첨부하거나, 또는 그 안에 있는 가장 오래된 저장소와 최신 저장소에 대한 부하의 상대위치(슬롯)을 달면 알 수 있다. 
