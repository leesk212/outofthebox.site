---
tags: 🌟paper-review security-attack csca
toc: True
---
# Abstract
* L1 Cache 이외에 FPU register file, store buffer에서의 유출이 확인되었다.
* Zombie load는 processor의 이전에 *unexlpored한 fill-buffor logic*을 공격한다.
* *Faulting Load Instruction*은 일시적으로 권한이 부여되지 않는 (이전에 current or sibling logical core에 의해 fill buffer로 로드가 되어진) 목적지를 역참조할 수 있다.
* 그럼으로 logical core들 사이에서 최근에 로드되어진 *stale value*를 유출 시킬 수 있다.
* 이것을 막을 방법은 현재(2018) *hyperthreading*을 끄는 것 뿐이다.

# Instruction
* Meltdown은 user space와 kernel space의 강력한 isolation을 통해서 보완이 되었지만 근본적인 원칙은 transient-execution attack이 있다는 것이 판명되었다.
* L1 cache뿐만 아니라 microarchitecture(register file,line-fill buffer, concurrent work, store buffer)에서의 데이터 유출 가능성을 확인할 수 있다.

## morden processor들은 구조적 equivalence를 유지하기 위해 모두 *re-order instruction*을 수행할 수 있다. 
  * re-order instruction을 통해서 분기문(exception)이 발동 되기전에 미리 명령어를 실행시키는 것이다.
  * 그리고 분기문에 해당되지 않는 명령어들은 roll back되어서 버려지게 된다.
  * roll back이 되게 됨으로써 architecture에는 영향이 없지만, side effect가 microarchitecture에 남게된다.
  * meltdown은 이러한 공격적인 행동 최적화를 *out-of-order*을 통해 data를 exploit하게 된다.
  
## Zomibeload에서 unexplored microarchitecture buffer를 보여주며, mircoarchitecture과 architecture의 결함(fault)가 추출될 수 있음을 보여준다.
  * microarchitecture faults: faults that cause a memory request to be *re-issued* internally without ever becoming architecturally visible.
  * meltdown같은 공격들이 architecture exception(page fault)같은 것 없이 일어날 수 있음을 증명했다.
  * 즉 fill buffer logic을 공격하는 meltdown attack --> Zombieload

* Zombieload는 내부적으로 *re-issued*되어야만 하는 load instruction들이 처음에 일시적으로 *(현재 또는 sibling hyperthreading으로부터)(이전 메모리 operation들에 속한)stale value*를 계산하는 것을 exploit한다.
* Using established trasient execution attack techniques, adversaries can recover the values of such "zombie load" operations.

## Zombie load reveals recent data values without adhering to any explicit address-based selectors.
  * --> Novel type of microarchitecture *data sampling*
  
## DataSampling
  * Missing link between traditional memory-based side channels 
  * Which correlate data address within a victim execution and existing Meltdown-type transient execution attacks that can directly recover data values belonging to an explicit address.
* We combine primitives(기존의) from *traditional side-channel attacks* with incidental(부수적인) *data sampling*

## Can attack
  1. leak accross processes, 
  2. (privilege boundaries)
  2. accross logical CPU cores
  3. Intel SGX enclave secrets ( loaded from a sibling logical core. )  
    * Extract sealing keys from Intel's architectural quoating enclave  
    * Breagking SGX's confidentiality  
    * Remote attestation guarantees.  
  4. Virtualiztion bounaries
    * hypervisor  
    * diffent virtual machines running on a sibling logical core.

## Prevent
  * Disabling hyperthreading ( Flushing several microarchitectural states during context switches )
  
  
# Background
## Transient Execution Attack
### Out-of-order excution
  * CPU가 다른 execution unit을 병렬적으로 사용할 수 있도록 해주기위한 성능이다.
  * 명령어들은 in-order로 모두 micro ops에 의해서 decoding 된다음에 이것은 operand가 준비된 것 부터 먼저 명령을 실행시킨다.
  * 그리고 그것들을 *reoder-buffer*에 중간 결과값을 넣어주고 명령어들이 기존의 stream과 동일시 할 수 있도록 보장해주기 위해서 기다린다.
  * 만약 특정 명령어들이 fault라면 명령어들을 retire시키고 pipeline을 flush해주고 모든 micro ops 결과물들을 reorder buffer에서 제거시킨다.
### Speculative execution
  * Instruction Pipleline이 조건문이 해결되기 전까지 stalling되는 것을 피하기 위해서 실행되어진다. 
  * 프로세서는 다음 값을 예측하여서 미리 실행시키고 reorder-buffer에 넣어둔다. 그리고 특정경우에 틀릴 경우, out-of-order과 마찬가지로 flush를 시킨다.
* 이 두개의 execution은 틀릴 경우를 대비해서 architecture에 commited 되지는 않는다.
* commit 되지는 않지만 명령어가 실행은 되었기 떄문에 측정될 수 있는 side effect가 발생된다.
* 그리고 이 side effect를 추출을 통해서 Transient Execution Attack을 실행시킨다.
* 전형적으로 이런 공격들은 cache 기반의 covert channel이 있게 되며, secret value를 추출시키는 것이 가능하게된다.

## Memory Subsystem
### Cache
* Memory access의 performance를 늘리기 위해서 사용된다. 
* 가장 자주 사용하는 것을 가장 빠른 internal cache(L1,L1D,L1I,L2)에 넣어두며, Cache들은 전형적으로 각 코어마다 multivel로 구성이 되어있고 그들을 공유(LLC)하는 것또한 존재한다.
* Modern CPU는 전형적으로 n-way set associative cahces들을 사용한다. (containing n cache lines per set)(each typically 64B wide)

### Virtaul Memory
* CPU는 process들의 memory isolation을 위해서 virtual memoryfmf tkdydgksek.
* Virtual memroy는 Multi-level translation table들을 통해 physical memory location으로 변환된다.
* Translation table은 access control, memory type, referenced memory region의 속성들을 갖고 있다.
* CPU는 TLB라는 buffer를 갖고 있어, Translation을 위한 Cache또한 갖고 있다.

### Memory Order Buffer
* micro ops는 dedicated execution units에 의해서 handled되어진다. 
* Intel CPU는 전형적으로 2개의 unit을 갖고 있는데, 
  * reorder buffer는 dependency를 제거시켜주고, 
  * memory order buffer,load buffer, store buffer는 dispatche of memory operation들을 control하며, 
  * memroy dependency를 resolve하기 위해서  그들의 progress를 track한다. 

### Data Load
* Load operation들을 dispatch(준비상태에서 실행상태로 이동)하기 위해서 entry들은 load buffer와 reorder buffer에 할당되어진다.
* 할당된 load buffer entry는 operation들에 대한 정보들(ordering constraints, reorder buffer id, the age of most recent stre) 갖고있다.
* Physical address를 결정하기 위해서
  * 상위 36bit는 memory management unit에 의해서 변환되어진다.
  * 동시에 변환되어지지 않는 하위 12bit는 L1D에 있는 cache set을 index하기 위해서 사용되어졌다.
* 만약 TLB에 물리 메모리 주소가 즉시 사용 가능하다면 바로 쓰지만 그렇지 않다면 PHM(Page miss handler)가 active 되어서 page-table walk를 실행하게 된다. 그리고 permission check와 함께 address translation이 시작되게 된다. 
* Physical address와 함께 tag그리고 way-of the cahce가 결정되어진다.
* 만약 요청된 data가 L1D에 있다면 cache hit가 발생하게 되고, load operation은 성공 되어진다.
* <mark>하지만 L1D에 없다면 LFB(line fill buffer)를 통해서 더 높은 계층의 메모리로부터 값을 받게된다.</mark>
* LFB는 다른 cache들과 main memory를 위한 interface로 사용되며, outstanding load를 track한다.
* Uncachable memory region의 memory 접근, 그리고 non-temporal moves 모두 LFB를 겪게 된다. 
* 만약 load 명령어가 load buffer 안에 있는 이전의 load operation의 entry를 위한 반응값과 같다면 load는 합쳐지게 된다.
#### Fault
* 만약 물리 메모리가 사용하지 못하는 것으로 fault가 일어나게 되어도, page table work는 즉시 abort되지 않을 것이다.
* pipeline에 실행에서 fault의 일어남과 상관없이 각가의 stage에서 undergo 되었을 것이고, fault의 경우 re-issued되기 때문이다. (reissued의 정의)
* 단지 마이크로 옵스의 faulting의 retirement에서 fault는 handled되어지고 pipeline은 flushed되어진다.
* 만약 fault가 load operation과 함께 일어나진다면 이것은 MOB에 valid and completed로 marking이 될 것이다.

## Processor Extensions
### Microcode
* 처음에 모든 명령어들은 CPU core에서 hardwired되어있었다
* 하지만 더 많은 복잡한 명령어들을 지원하기 위해서 microcode 는 더 높은 단계의 명령어들을 multiple hardwarelevel instruction들을 사용하면서 구현가능하게 했다.
* 중요하게 이것들은 processor vendor 가 complex behavior를 지원하도록 허락해주었고, 심지어 CPU behavior를 확장하고 수정할 수 있도록 해주었다.
* 새로운 architecture들은 이런 mircocode extension을 이용하게 해주었다. (intel SGX)
* execution unit이 hardware에서 더 빠른 path로 수행되는 동안, 더 복잡하고 느린 path의 operation들은 전형적으로 microcode assit에 의해서 수행되었다. 
  * microcode assit points the sequencer to a predefined microcode routine. (microcode assit는 sequencer가 미리 정의된 microcode routine을 가리키도록 한다.)
* 그렇기 위해서, execution unit은 event code를 faulting micro-op의 결과와 함께 연관시킨다.
* <mark>micro-op의 execution unit이 commited 되어질때 event code는 out-of-order scheduler가 re-order버퍼에 모든 in-flight micro-ops를 squash하도록 진행시켜준다.</mark>
* microcode sequencer는 event code를 사용한다. microcode에 있는 이벤트와 연관된 micro ops를 읽기 위해서

### Intel TSX
* Intel TSX는 hardware transaction memory[Intel Haswell CPU에서 소개되어진]를 지원하기 위한 x86 instruction set extension이다. 
* TSX와 함께 특정 코드 부분은 transactionally하게 수행되어진다.
* 만약 전체 코드 region이 성공적으로 수행된다면 memory operations은 다른 logical process에 commit되어진다.
* 만약 transaction동안 issue가 발생된다면 tarnsaction abort는 execution을 모든 수행된 명령들을 버리고, 이전 transaction이 실행되던 곳으로 roll back시킨다. 
* Transaction abort는 다른 다른 문제를 야기시킨다. 
  * Conflicting되고 있는 메모리 operation이 발생하는데, 다른 logical proessor가 transatction에서 수정되어진 adress로부터 데이터를 읽고, 그리고 사용되고 있는 transaction에 그 데이터 값을 쓴다.
* 게다가 transaction에서 쓰이고 읽힌 데이터들은 성공적인 transaction을 위해서 LLC와 L1 cache 각각의 size를 초과할 수 없다.
* 몇몇의 instruction과 system event들은 transaction이 abort되는 원인이 될 것이다.

### Intel SGX
* Skylake microarchiture에서 Intel은 SGX를 발표하였다.
* isolating trusted code를 위한 instruction set extension
* SGX는 enclave안에서 trusted code를 실행시킨다.
  * enclave는 평범한 host application process의 virtual address space에 mapped되어있다.
  * 하지만 H/W 자체에 의해서 system의 rest(?)로부터 isolation되어있다.
* SGX 모델의 위험은 OS와 다른 running application이 타협될 수 있고 그리고 그래서 신뢰성이 없다고 가정한다.
* non-enclave mode에서 SGX enclave memory로의 접근하기 위한 모든 시도들은 abort page semantics를 결과를 보였다.
  * current privilege level에 상관없이 읽으면 0xff의 dummy 값을 return하고 쓰기를 하면 무시되어진다.
* 더 나아가서 memory bus를 탐지하는 강력한 물리적인 공격자들을 보호하기 위해서, SGX hardware들은 명백히 enclave로 사용되고 있는 메모리 region을 암호화한다.
* 수 많은 시간동안 연구자들은 다양한 방법으로 SGX를 유출시키고자 하였고, 최근에 SGX는 또한 transient execution attack(필수적으로 microcode update되고 security version number를 증가시키는)에 의해 compromised 되었다.
* 모든 SGX 유도되고 attestation된 SVN을 포함한다. 현재 microcode version을 반영하기 위해서 그리고 그러므로 securtiy level을 반영하기 위해서
#### Instruction
* 비허락된 OS가 vectoring하기전에 SGX는 enclave의 SSA(save state area)안에 CPU register를 안전하게 저장한다. 
##### eenter
* redirects control flow to an enclave entry point 
##### eexit
* transfers back to the untrusted host application
##### ersume
* restore processor state from the SSA frame and continue a previously interrupted enlcave
#### egetkey
* SGX-capable processsors feature cryptographic key derivation(유도) facilites (CPU level master secret에 기반되어서)
* a secure measuremnet of the calling enclave's initial code and data
* 이 키를 사용함으로써 enclave들은 안전하게 untrusted persistent storage에 secret들을 seal할수 있다. 
* 그리고 같은 프로세스에 거주하고 있는 다른 enclaves들과 secure commication channel을 설치할 수 있다. 
* 더 나아가서 원격 증명이 가능하기 위해서 intel은 trusted quoating enclave(unseal an Intel-private key)를 제공한다. 
* 그리고 비대칭증명을 local enclave를 넘어서 사용한다.


# Attack Overview
## Overview

* <mark>ZombieLoad는 현재 물리 CPU에 메모리로드의 값을 관찰하는 transient-execution attack이다. </mark> 
* <mark> *Fill buffer*를 exploit하는 Zombieload는 모든 물리 CPU core의 논리 CPU에 의해서 접근될 수 있고 그리고 processes들 사이에서, privilege level들 사이에서 구분되지 않는다.</mark>
* load buffer는 메모리 subsystem으로부터 모든 메모리 로드를 위해서 queue처럼 작동한다.
* CPU가 실행동안 memory load를 만나게 될때 이것은 load buffer에서 entry를 예약한다.
* 만약 load가 L1 hit가 아니라면 이것은 추가적으로 fill-buffer entry를 요청하게 된다.
* 요청된 데이터가 로드가 되었을 때 memory subsystem은 해당된 load와 fill buffer entry들을 free 시킨다. ( 이부분에서 사용된 load instruction은 retire 될 것이다)
* 하지만 우리는 관측했다. 복잡한 microarchitecture condition(e.g fault)을 얻는 것 아래에서 로드는 microcode assit를 요청하고, <mark>이것은 첫번째로 re-issued eventually가 발생되기 전에stale value를 읽는다.</mark>
* 이전의 meltdown류의 공격들과는 다르게 공격자가 특징지은 주소값에 기반하여 값을 유출시키지 못하고 <mark> 물리 cpu core에서 현재 로드된 모든 값들을 간단히 유출시킨다. </mark>
* 이것이 많은 제약이 있는 것 처럼 보이지만 새로운 영역의 강력한 공격이고, 전통적인 부채널 공격들과 합쳐졌을 때 심지어 더 강력해진다.

## Microarchitectural Root Cause
* Meltdown, Foreshadow, Fallout 동안 유출의 원천(source)는 분명해졌다. 그리고 납득할 수 있는 원천이 있다.
* 하지만 ZombieLoad는 완전히 분명하지 않다.
* 유출을 관측하기 위해서 우리가 필수적으로 만들어진 block을 확인면서, 우리는 가설을 제공할 수 있었다. 왜 만들어진 블럭들의 상호작용이 관측되는 유출을 이끌 수 있는지를
* 우리는 단지 인텔 CPU에서 데이터 유출을 관측할 수 있었기 때문에, 우리는 이것은 스펙터가 아니라 멜트다운류의 구현 이슈라 가정했다.
* 우리의 가정을 위해서 우리는 우리의 관측과 최근에 존재하지 않는 fill buffer의 공식문서를 결합하였다.
* 궁극적으로 우리는 우리의 가설을 증명하지도 증명않하지도 못했다. 

### <mark>Stale-Entry Hypothesis</mark> 
* 모든 LOAD는 load buffer의entry와 잠재된 fill buffer의 entry와 연관되어있다.
* load가 복작합 상황을 만났을때 (fault)같은 이것은 micro assist를 요구한다.
* 이 micro assist는 machine을 깔끔하게 비워주는데 도움을 준다.(pipe를 flush하면서)
* pipeline이 flush 되는 과정에서 이미 실행중인 명령어들은 여전히 실행을 완료하려고 합니다.(실행하고 있습니다.)
* 이것은 추가적인 delay를 발생시키지 않기 위해서 가능한 빨리 일어나야만 하는데, 우리는 fill-buffer entries와 긍정적으로 물리주소의 부분과 가능한 일치할 것으로 예측합니다. 
* 그럼으로 load는 이전 로드에 유효했던 잘못된 fill-buffer entry로 계속됩니다.
* 이것은 하드웨어에서 free이후 사용 취약점을 야기합니다. 
* 인텔은 fill buffer를 hyperthreading에서 경쟁적으로 공유된 것으로써 fill buffer를 기술합니다.
* 두개의 논리 코어들이 전체 fill buffer를 얻음으로써
* 결과적으로 stale fill buffer entry는 또한 형재 논리코어의 이전에 로드된 것으로부터 될 수 있다.
* 결론적으로 load instruction은 이전에 로드로 부터 타당한 값을 로드하게 된다.

### Leakage Source
* 유출되는 데이터의 가능한 원친들을 줄이기 위해서 두가지의 실험을 제안했다.
* 우리의 첫번째 실험에서 우리는 uncachable 페이지들을 marked했다. (page-table entry)를 통해서 그리고 케시로부터 이 페이지들을 flushed 시켰다.
* 결론적으로 모든 메모리 로드는 모든 cache 레벨을 피하고 메인메모리로부터 fill buffer로 이동이 된다.
* 우리는 그러고 uncacheable memory page에 secret를 쓴다. 캐시에 data의 copy가 없는 것을 보장하기 위해서
* uncacheable memory page로부터 데이터를 로딩할때, 우리는 leakage를 보게되었다. 하지만 leakage rate는 단지 byte/s 정도 밖에 되지 않았다. (5.91B/s) 
* 우리는 이 유출을 fill buffer의 탓으로 돌릴 수 있었다.
* 이것은 또한 동시 작업성에서 exploited되었다.
* 우리의 가설은 <MEM_LOAD_RETIRED.FB_HIT performace counter>에 의해서 뒷받침 되었다.
* 이것은 수천 비트의 line-fill-buffer hit를 보여주었다.
* Intel은 주장했다. 이 유출은 단지 fill buffer로부터있다고, 하지만 우리의 두번째 실험은 보여주었다. line-fill buffer가 단지 유출의 source가 아닐 것이고
* 우리는 Intel TSX를 언급한다. 메모리의 접근이 line-fill buffer를 도달하지 못하는 것을 보장하기 위해서 다음과 같이!!
* Transaction안에서 우리는 첫번쨰로 secret Value를 그리고 이전에 다른 값으로 초기와 되었던 메모리 location에 쓴다. 
* Transaction안에서 쓴 값은 보장한다. 주소값은 transaction의 write set에 있고 그리고 그럼으로 L1에 있다고!!
* Cache로 부터 write set에서 data를 추출하는 것은 transaction abort를 만든다.
* 그럼으로 모든 연속적인 write set으로부터 data까지의 메모리 접근은 보장한다. 이것이 L1으로부터 받아졌고 그리고 그래서 line-fill buffer로 보내져지지 않았다는 것을.
* 이 실험에서 우리는 높은 비율의 유출을 관측했고 이리고 이것은 KB/s이었다.
* 더 중요하ㅓ게 우리는 단지 TSX transaction에서 쓰여진 값 뿐만 아니라, transaction이전에 메모리 loaction에서 쓰여진 값을 볼 수 있었다.
* 우리의 가설(line-fill buffer가 Performance Counter를 관측하는 것에 의해서 뒷받침되어지는 것)은 단지 leakge의 원천뿐이 아니다.
* MEM_LOAD_RETured.FB_HIT 그리고 MEM_LOAD_RETIRED.L1MISS performance counter들은 충분하게 오르지 않았다.
* 반면에 MEM_LOAD_RETIRED.L1_HIT performance counter는 수천의 L1 Hit를 보여주었다.
* victim core에서 데이터 유출을 평가하는 동안, 우리는 Attacker core에서의 performace counter인 MEM_LOAD_RETIRED.FB_HIT를 관측하였다. 
* 만약 주소값이 cached되었다면 우리는 Peason correlation(rp = 0.02)와 관계있음을 관측할 수 있었다. correct recoveries와  line-fill buffer hits들 사이에서, no association을 나타내면서
* 하짐나 victim core에서 데이터가 flushing되고 있는 동안에, 연속적인 접근이 LFB를 통과해야만 하기에 ,우리는 강력한 연관관계(rp= 0.86)를 관측하였다.
* 이결과는 단지 line-fill buffer가 leakage의 원천이 아니라는 것을  나타낸다 
* 하지만 다른 설명은 performance counter가 이런 corner cases에서 reliable하지 못하다는 것이다.
* Future work는 이것은 조사 해야만한다.!!

### Classification
* 이 섹션에서 우리는 memory-based side channel와 transient-execution attack을 classify하는 방법을 소개한다.
* 모든 이러한 공격 동안에 우리는 가정한다. 타겟 프로그램들이 memory operation을 실행한다. 프로그램의 현재 instruction pointer에서 특정 주소에서 특정한 데이터 값들과 함께. 
* 
