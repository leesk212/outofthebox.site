---
tags: linux 
toc: True
---

# rpm2deb: alien
> ref: <https://junhyunny.github.io/information/linux/linux-alien/>

* $ sudo apt install alien
* $ sudo alien -d xxxx.rpm 
* xxxx.deb


# deb 분석 (dpkg -x)
> ref: <https://devanix.tistory.com/223>

* $ dpkg -x xxx.deb {directory}
* directory의 권한을 따지는 경우가 존재한다. 
해당 파일의 위치에 권한된 디렉토리의 위치에 압축해제한다.
