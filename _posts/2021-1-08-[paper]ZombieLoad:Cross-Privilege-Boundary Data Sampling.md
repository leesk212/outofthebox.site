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
