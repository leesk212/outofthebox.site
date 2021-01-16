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
* LFB는 다른 cache들과 main memory를 위한 interface로 사용되며, 현명한 
