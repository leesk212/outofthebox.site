---
tags: linux
toc: True
---
# pcm  
> sudo ./pcm.x  


pcm은 msr이라는 레지스터에서 core의 정보들을 받아와서 상태를 보여주는 머플리케이션이다.  
그럼으로 레지스터에 직접적인 접은을 해야하기 때문에, sudo 권한이 꼭 필요하다.  
# modeprobe msr  
> sudo modeprobe msr   


modeprobe는 레지스터를 커널에 적재시켜주는 명령어이다.   
그런데 pcm을 실행시키기 위해서는 msr 레지스터가 적재가 되어야하는데 msr 레지스터가 자동으로 적재되어 있지 않기 때문에
pcm을 실행시킬때 msr 레지스터를 커널에 적재시켜주는 것이다.  
# pcm code  
* https://github.com/opcm/pcm
