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
  
  
