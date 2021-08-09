---
tags: 🌟paper-review security-defense cache-randomize
toc: True
---
# Cache Randomized
* CEASER: http://memlab.ece.gatech.edu/papers/MICRO_2018_2.pdf
* CEASER-S: https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=8980326
* ScatterCache: https://www.usenix.org/system/files/sec19-werner.pdf
* MIRAGE: Mitigating Conflict-Based Cache Attacks with a Practical Fully-Associative Design: https://arxiv.org/pdf/2009.09090.pdf

# Conflict-based Cache Attacks
![image](https://user-images.githubusercontent.com/67637935/125395303-fa393080-e3e5-11eb-9e13-7f016c77af17.png)
## Definition
> In conflict-based attacks, the attacker tries to determine the cache sets have been accessed by a victim program. For example, in the Prime+Probe attack [13], the attacker fills a cache set with its own lines (Prime step), waits for the victim to perform its accesses (wait step), and then accesses the set again to determine which cache sets have been accessed by the victim (Probe step). Figure 2 shows an example of such a Prime+Probe attack on a two-way cache. The attacker places lines A and B in Set 0, and waits. The victim accesses a line (say line V) that maps to Set 0, which evicts line B. At a later time, the attacker can access A and B, and measure the time. Given the long-latency now required for B, the attacker can infer that the victim accessed Set 0. Knowing the access pattern of an application can leak secret information [3].
