# Abstract
* 지금까지의 cache side channel attack은 메모리의 접근의 유무를 통해 정보를 유출시켰다.
* 그렇기에 cache hit값이 또는 Cache miss값이 비이상적으로 올라가게 된다. 
* 이는 HPC(Hardware Performance Counter)를 통해 탐지가 가능하며 이를 통해 Flush+Reload,RowHammer,Prime+Probe등의 공격의 detection이 가능하다.
* 그러나 Flush+Flush는 memory access가 이루워지지 않고 clflush execution time을 통해 data가 cached되었는지 안되었는지를 판단하기 때문에 HPC의 탐지가 불가능하다. (Stealthy의 이유)
* 또한 Flush+Flush는 다른 cache side channel attack과 비교하여 빠른 실행성을 갖고있는데, 그 이유는 메모리 접근이 없고 clflush 명령어만 사용해 공격을 진행하기 때문이다. (Fast의 이유)


# Introduction
* Cache attacks include covert and crytographic side channels, **but** caches have also been exploited in other types of attacks such as bypassing kernel ASLR, detecting cryptographic libraries, or key stroke
* HPC->OS-level-detection based on cached hit and misses
* three scenarios
  * a covert channel
  * a side-channel attack on user input
  * a side-channel attack on AES with T-tables
* Implement a detection machanism that monitors cache references and cache missess of LLC
* The Flush+Flush attack does not trigger **prefetches** and thus allows to monitor **multiple addresses** within a 4KB memory range in contrast to Flush+Reload that fails in these scenarios.

# Background
## CPU Caches
* CPU caches hide the memory access latency to the slow physical memory by buffering frequently used data in a small and fast memory
* CPU achitectures: n-way-set-associative-caches(->cache sets->cache lines) 
* A line is loaded in a set depending on its address, and each line can occupy any of the n ways.
* L1,L2,L3( inclusive ) ( L1, L2에 있는 모든 데이터들은 L3에 있음 )( 그렇기에 다른 프로세스의 L1 cache에 있는 중요한 data를 다른 프로세스에서 볼 수 있음 )( 추출되게 되면, 이를 cache attack이라 부름 )
* **LLC는 ring bus의 형태로 코어들에 의해서 많이 나누어져있음** 
![Ringbus](https://www.researchgate.net/publication/339991541/figure/fig2/AS:870238061076481@1584492330268/Architecture-of-LLC-that-consist-of-LLC-slice-connected-via-mesh-inter-core-bus-among-CPU.png)
* **Sandy Bridge에서는 각각의 물리적 주소값들을 ring-bus의 형태로 나뉘어진  LLC에 "Complex-address function"을 통해 mapping을 진행한다.**
* Cache replacement policy
 * variants of LRU
 * bimoal insertion policy(CPU can switch between the two strategies)

## Shared memory
* OS & hypervisors instrument shared memory to reduce the ovevall physical memory utilization and the TLB utilization
* OS는 file을 mapping하는 것, 프로세스는 fork하는 것, 그리고 process를 두번 실행하는 것 모두 비슷하게 처리된다. (왜냐하면 메모리 지역에 대한 중복 제거 결과이기 떄문에)
* Content-based page deduplication ( OS & hypervisor는 물리 메모리에 byte단위로 동일한 페이지를 스켄하고 동일한 페이지들이 같은 물리 메모리에 mapping되어있다면 **동일한 실제 페이지에 다시 매핑되고 다른 페이지는 사용 가능한 페이지로 표시된다** **이 기술은 TLB와 물리메모리의 사용 성능을 저하시킨다**
* 관련없는 정보들의 공유와 **sandboxed** process들 사이, 그리고 다른 가상머신에서의 진행중인 프로세스들 사이에서의 메모리 공유는 보안의 걱정을 불러일으킨다.

* 
