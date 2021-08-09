---
tags: 🌟paper-review security-attack csca
toc: True
---
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

## Cache Attacks and Rowhammer
* Cache Attack은 CPU cache와 물리메모리 와의 다른 지연시간의 차이로 발생하는 시간차이에 대한 공격이고 전형적으로 두가지로 나눠진다.
  1. Prime+Probe (메모리 공유가 되어있지 않는 것)
  2. Flush+Reload (메모리 공유가 되어있는 것)
* Prime+Probe
  1. 공격자가 cache set을 점령한다.
  2. 피해자가 cache set 된 line을 교체 하는 것을 측정한다  
  * **현대 프로세서들이 complex addressing과 undocumneted replacement 정책을 사용한 물리적으로 색인된 llc를 이용하기 때문에 Cross-VM side-channel attack과 covert-channel들이 나타나게 되었다.** 
* Flush+Reload
  1. 공격자가 clflush로 일정 single cache를 flush 시킨다.
  2. 공격자는 계속 접근한다.
  3. 만약 피해자가 single cache에 접근을 했으면 공격자가 접근한 시간이 짧고 (cache hit) 피해자가 single cache에 접근을 하지 않았으면 (cache miss) 공격자가 접근한 시간이 길다.
* Rowhammer
  * 전형적인 cache attack은 아니다.
  * 특정 DRAM row에 계속 반복적으로 접근하게 되면 인접한 메모리에서 random bit flip이 일어나는 취약성을 사용한다. 
  * [Rowhammer_attack](https://medium.com/@Anna_IT/rowhammer-%EA%B3%B5%EA%B2%A9-%EB%8C%80%EC%9D%91%EC%9D%84-%EC%9C%84%ED%95%9C-ecc-%EB%A9%94%EB%AA%A8%EB%A6%AC%EC%9D%98-%ED%9A%A8%EA%B3%BC%EB%8A%94-error-correcting-code%EC%9D%98-%EC%8B%A4%ED%9A%A8%EC%84%B1-%EA%B2%80%EC%A6%9D-36856febbb57)
  * 이러한 접근들은 DRAM에 도달하기 위해서 모든 cache들의 level을 통과해야만하고 bitflip을 유발시킨다. 
  * 취약점이 추출된 공격들은 이미 root권한을 얻는 것을 증명했고, sandbox를 파괴하는 것을 증명했다.
  * Rowhammer은 충분한 수의 cache hit와 cache missess를 증명했고, 이는 Cache Side attack과 닮았다.
  
# The Flush+Flush Attack
* cache miss를 만들지 않고, 매우 적은 양의  cache hits들을 만든다.
* Flush+Reload와 SW,HW의 같은 스펙에서 발생이 진행될 수있다.
* Attack은 무한의 루프에서 실행되면서 실행되고, 계속적으로 타겟된 공유 메모리 라인에게 clflush 명령어를 실행한다. 
* 공격자는 clflush 명령어의 실행시간을 측정한다.
* 실행시간을 통해 이 메모리 라인이 cached 되었는지 아닌지를 파악한다.
* attacker가 캐시로부터 메모리라인을 로드하지 못했다면 다른 프로세서가 이것을 로드했는지 안했는지를 드러낸다.
* 동시에 clflush는 캐시에서 다음 공격 루프라운드를 위해서 메모리 라인을 추출한다. 
* 측정은 rdtsc 명령어로 cycle을 측정한다.  
![aaa](https://media.springernature.com/original/springer-static/image/chp%3A10.1007%2F978-3-319-40667-1_14/MediaObjects/416839_1_En_14_Fig1_HTML.gif)
* 다음표와 같이 cached가 되어있을때와 아닐때 약 12cycle정도 차이가 남을 확인할 수 있다.
* Flush+Reload보다 cycle수가 차이나는 것이 적음으로 본질적으로(inherently) Flush+Flush 공격은 정확도가 적음을 확인할 수 있다. 
* 그러나 부채널 공격을 통해 같은 양의 정보를 추출한다고 했을 때 그 속도는 확연하게 빠른 것을 확인할 수 있다.
  
# Detecting Cache Attacks with Hardware Performance Counters
* Cache hit과 Cache miss가 급격하게 많아지는 것은 HPC(OS level)에서 탐지될 수 있다.
* 하지만 공격을 **막기** 위해서는 공격중인 프로세스를 확인하는 작업이 필수적이다.
* 그래서 Flush+Flush 공격은 이 확인되어지는 과정을 할 수 없게 stealthy하다.
* HPC는 특별한 목적(특별한 H/W의 상태를 관측할 수 있는)의 register이다. 
* **HPC는 LLC에서의 cache references와 cache miss를 관측할 수 있다.**
* Performance tunning을 위해 만들어졌지만, 현재 Flush+Reload와 Rowhammer를 탐지하는데 적합한 register가 되었다.
* 하지만 Flush+Flush 공격은 Performance counter들로는 탐지가 실현불가능하다.
* Linux의 perf_event_open systemcall interface로 이용되는 것을 분석할 수 있다. (이 시스템 콜은 사용가능한 perfourmance counters들의 subset(일부분)을 userspace에서 접근 가능하게 제공해주고, kernel단에서 수행된다.)(그리고 이 레지스터들은 현재 공격중인 것을 탐지하는데 쓰인다)
* 23개의 h/w와 cache performance events를 분석하였고, 추가적으로 C-box라고 불리는 **uncore performance monitoring unit**을 분석하였다. (c-box는 clflush 명령어와 직접적으로 연관되어, cache hits와 miss에 대한 것을 표시해준다.)
  * UNC_CBO_CACHE_LOOKUP event는 LLC의 cache slice를 보는 것을 허락하는 register이며C-Box monitoring unit은 포괄적인 interface로는 사용할 수 있을 뿐만아니라 특별한 레지스터로써도 사용할 수 있다.
  * ITLB performance counters를 사용했다. 왜냐하면 Flush+Reload와 Rowhammer 공격들은 모두 많은 수의 LLC CACHE MISS를 일으키고 작은 부분의 코드에서만 실행되기 때문이다. 작은 코드를 실행하는 것은 ITLB에 적은 압박을 야기하기 때문이다.
* 24개 중에서 4개정도만 오탐 없이 공격들을 찾아낼 수 있었다.
  1. CACHE_MISSES
  2. CACHE_REFERENCES
  3. L1D_RM
  4. LL_RA
* CACHE_MISSES와 CACHE_REFENCE로만으로도 충분하며 performace counter와 관련된 C값이 km또는 kr보다 더 크다면 공격이 되었다고 정의하였다. 
  * km , kr의 threshold는 malware의 maximum distance와 minimum value를 통해 만들었고,  기본 application의 maximum distance값을 확인해서 구하였다. 
  * km(cache miss, cache reference)
* Flush+Reload, Prime+Probe, Rowhammer은 정상적으로 탐지가 되었지만, Flush+Flush는 cache miss와 cache reference를 일으키지 않음으로 탐지되지 않는다.

# Covert Channel Comparison


# Side-Channel Attack on User Input


# Side-Channel Attack on AES with T-Tables

##### AES  공부


# Discussion

## Using clflush to Detect Cores and Cache Slices

## Countermeasures

# Related work

## Detecting and Preventing Cache Attacks

## Ussage of Hardware Performance Counters in Security

## Cache Covert Channels

## Side-Channel Attack on User Inputs

# Conclusion

  
  
