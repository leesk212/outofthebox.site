---
tags: linux
toc: True
---
linux가 설치되어 있는 디스크를 찾는다.

대부분 busybox 호출 log를 보면 /dev/sd? 로 설정 되어있을 것이다.

나같은 경우는 리눅스가 /dev/sd5/에 설치가 되어있었다. 

> (initramfs)fsck -f /dev/sd? 

> 복귀를 하시겠냐는 yes no의 표시가 나오게되는데, 이때 yes를 계속 눌러주면 된다.

> (initramfs)fsck -f /dev/sd?

busybox가 호출되는 이유는 정상적인 리눅스 종료가 안되었기에 안전모드(?)로 부팅이 되었다고 비유하면 될 것도 같다(?)
-llvm 설치 너무 안된다..
