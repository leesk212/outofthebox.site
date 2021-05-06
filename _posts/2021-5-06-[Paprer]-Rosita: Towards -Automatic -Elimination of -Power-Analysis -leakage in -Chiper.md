# Rosita
* A code rewrite engine that uses a leakage emulator which we amend to correctly emulate the micro-architecture of a target system.
> 대상 시스템의 마이크로 아키텍처를 올바르게 에뮬레이터로 수정하기 위해 리크 에뮬레이터를 사용하는 코드 재작성 엔진이다.
* AES, ChaCha, Xoodoo등을 적은 패널티로 protected mask하였다. 
## Uniformed distributed --> only proven secure attack 
* To fix this leaks ``` repeatedly "Tweak the code until it stops leaking ```
> 반복적으로 누출이 멈출 때까지 코드를 수정한다.
* We have set out to explore if leakage emulators can be used for automatic elimination of side channel leakage from software implemenations
> 소프트웨어 구현에서 사이드 채널 누출을 자동으로 제거하기 위해 누출 에뮬레이터를 사용할 수 있는지 조사하기 위해 착수했습니다.
* Code rewrite program: "ROSITA" + Extended leakage emulator: "ELMO"
