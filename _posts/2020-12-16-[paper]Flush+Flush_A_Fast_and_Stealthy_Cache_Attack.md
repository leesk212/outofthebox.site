# Abstract
* 지금까지의 cache side channel attack은 메모리의 접근의 유무를 통해 정보를 유출시켰다.
* 그렇기에 cache hit값이 또는 Cache miss값이 비이상적으로 올라가게 된다. 
* 이는 HPC(Hardware Performance Counter)를 통해 탐지가 가능하며 이를 통해 Flush+Reload,RowHammer,Prime+Probe등의 공격의 detection이 가능하다.
* 그러나 Flush+Flush는 memory access가 이루워지지 않고 clflush execution time을 통해 data가 cached되었는지 안되었는지를 판단하기 때문에 HPC의 탐지가 불가능하다. (Stealthy의 이유)
* 또한 Flush+Flush는 다른 cache side channel attack과 비교하여 빠른 실행성을 갖고있는데, 그 이유는 메모리 접근이 없고 clflush 명령어만 사용해 공격을 진행하기 때문이다. (Fast의 이유)


#Introduction
* Cache attacks include covert and crytographic side channels, **but** caches have also been exploited in other types of attacks such as bypassing kernel ASLR, detecting cryptographic libraries, or key stroke
* HPC->OS-level-detection based on cached hit and misses
* three scenarios
  * a covert channel
  * a side-channel attack on user input
  * a side-channel attack on AES with T-tables
* Implement a detection machanism that monitors cache references and cache missess of LLC
* The Flush+Flush attack does not trigger **prefetches** and thus allows to monitor **multiple addresses** within a 4KB memory range in contrast to Flush+Reload that fails in these scenarios.
