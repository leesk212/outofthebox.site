---
tags: CPU
toc: True
---

ref 1: <https://m.blog.naver.com/cinsky/221723960849>

ref 2: <https://github.com/xmrig/xmrig/issues/1413>

# Hardware Prefetcher

CPU의 L2와 DRAM 간의 속도 차에 의한 시간 지연을 하드웨어적으로 보완해 주는 옵션.
즉, 당연하게도 CPU의 L2 캐쉬가 DRAM 보다 입출력 속도가 빠르다. 
CPU가 일을 하려는데 L2에 원하는 데이터가 없을 때 DRAM에서 불러와야 하는데 그동안 CPU는 쉬어야 한다.
이를 보완하기 위해 미리 사용될 꺼라 예상되는 데이터를 L2로 가져다 놓는 걸 말한다.
물론, 예상되는 데이터를 가져올 수도 쓸모 없는 데이터를 가져올 수도 있다.
빈번한 L2와 DRAM 간의 입출력이 되려 전체 성능을 저하 시킬 수 있겠지만 요점 하드웨어의 캐쉬 적중율은 높기 때문에 성능상 손해볼 일은 아니라고 생각하기 때문에 "Enabled"로 설정한다.

# Adjacent Cache Line Prefetch

의미만 보면 인접한 캐쉬 정보까지 가져오겠단 얘기
Disable 하면 64bytes의 Cache Line을 통해 데이터를 가져오고 Enable하면 128bytes의 Cache Line을 통해 데이터를 가져온다는 뜻
굳이 Cache Line을 줄일 필요는 없으니 Enable로 설정한다.
PC급이 아닌 서버에서는 Disabled로 설정해야 성능 저하를 막을 수 있다고 하는데 서버 쓰는건 아니니까 왜 저하되는지 파고 들지 않았다
