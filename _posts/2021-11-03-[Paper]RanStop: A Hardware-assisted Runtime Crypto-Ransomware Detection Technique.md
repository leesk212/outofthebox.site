---
tags: 🌟paper-review security-attack detection
toc: True
---

# Title
RanStop: A Hardware-assisted Runtime Crypto-Ransomware Detection Technique

# Abstract

* Crpyto-Ransomware: 널리 퍼진 많은 맬웨어 중에서 크립토 랜섬웨어는 문서의 무단 암호화를 통해 액세스 거부를 생성하고 문서를 인질로 잡고 재정적으로 갈취함으로써 영향을 받는 사용자에게 금전적 갈취를 가하는 심각한 위협이 됩니다.

* 랜섬웨어의 다양한 변종은 *정적 실행 서명*에 의존하는 안티바이러스 및 소프트웨어 전용 맬웨어 탐지 체계로부터의 "회피" 기능과 함께 그 수가 증가하고 있다.

* 해당 논문은 Hardware-assisted scheme인 Ranstop을 고안하였으며, 이는 상용프로세서(Commodity Processors)에서 crypto-ransomware의 감염을 초기에 탐지한다.

* 세부적으로, 마이크로아키텍처럴 이벤트를 관찰가능한 현대프로세서에 장착된 PMU 내부의 HPC를 w정보들을 leverage한다. 그리고 잘알려진 또는 잘 알려지지 않은 cypto-ransomware를 탐지한다.

* 우리는 LSTM을 사용한 RNN학습을 진행한다. H/W 도메인에서 micro-architectural event를 분석하기 위해서, 다수의 ransomware의 변종들과 일반 프로그램을 수행할때

* 우리는 HPC관련한 정보를 사용하는 "intrinsic statistical features"를 개발하기 위해 timeseries를 만들었다. 그리고 LSTM과 GAP을 사용하여 RanStop의 탐지 정확도를 높히고 noise를 제거하였다.

* RanStop은 정확하고 빠르게 ransomware를 2ms내에 잡아낼 수 있다. 각각 100us apart로 20timestamps씩 HPC 정보들을 분석함으로써 Program Execution 시작으로 부터  

* Ranstop은 충분히 damage를 탐지하기에 빠르며 50회이상의 무작위 시도 중에서 약 97%의 정확도가 나왔다. 
