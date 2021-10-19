---
toc: True
tags: window tensorflow cuda
---


# 1. cuda를 설치한다. (nvidia-driver를 설치하는 것이 아니다.)
* Nvidia GTX,RTX 별 지원가능한 cuda 버전 확인: <https://developer.nvidia.com/cuda-gpus#compute>


# 2. 사용하고 싶은 tensorflow 버전에 따라서, cuda를 지원하는 cudnn를 설치한다.
* Tensorflow 버전 별, cuda cudnn 확인: <https://www.tensorflow.org/install/source_windows#tested_build_configurations>
* cudnn 설치 링크: <https://developer.nvidia.com/cudnn>
  * 로그인해야지 됨
* cudnn 설치 후 해당 cuda의 10.1v 내부의 디렉토리 위치에 덮어씌어주기
* 경로 확인하기 (IDE재부팅 해야 경로 정상 수정될 수도 있음)
* 7.6 쿠디엔엔 1.15랑 호환가능하다

## 경로 설정 안될때!!
> https://hansonminlearning.tistory.com/7

또 신기한 오류가 잘못 복사 붙여 넣어서 내부 bin폴더가 다 날라갔었다.

# 3. anaconda navigator에서 tensorflow 설치하고 싶은 버전 설치하기
* pip install tensorflow==2.3.0

# 4. IDE에서 Success dll 뜨면 tensorlfow with gpu supporting 성공!
