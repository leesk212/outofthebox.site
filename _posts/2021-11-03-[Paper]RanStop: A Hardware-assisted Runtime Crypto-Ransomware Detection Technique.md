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

# Introduction
* Ransomware는 금전적인 피해를 많이 야기하고, 그중에 가장 대표되는 것인 crpyto-ransomware는 현대 세계에서 가장 주가 되는 위협으로 대표된다.
* 현재 있는 Ransomware를 막고 탐지하기위해서 다양한 연구가 있었으며 대부분은 static software-centric set-up에 기반된 기술이다.
  * static software-centric set-up 
    1. signiture matching in the program control flow
    2. signiture searching for dominent features of malicious ransomware-like activites
    3. signiture monitoring high-level(software) execution, API/system calls, data to detect potentially anomalious behavior
* 해당 되는 방어기법을 피하는 큰 두가지 software-only mechanisms이 있다. (적절한 보호 기법을 갖고 있음에도 불구하고)
  * the latest ransoware attacks on the city of Atlanta's online shutting down the activites for more than six days
  * the WannaCry malware infecting many business organizations in over 150 different contries.
* 그리고 이런 software 전용 (보안) 체계는 다음과 같은 본질적인 제한에 고통을 겪고 있다.
  * There are numerous different ransomware with distinct and obscure control flow signatures that can evade static signature-matching anti-malware schemes.
  * Software-based techniques often require binary signature for each variant of the ransomware (or, in general, malware). It imposess huge overhead on the database, i.e., the size of anti-malware updates, with ever-growing polymorphic and metamorphic variants of a class of malware.
  * Even in the case of a successful detection, several existing schemes are too late (in time) at detecting a ransomware such that the victim system and important files (or directories) may already be maliciously corrupted(encrypted) and locked, where the possibility of the recovery is extremely rare.
  * The static signature mapping can produce a high rate of false decisions (false positive/negative) which pose critical impact on the smooth operation of the system.
  
* 그럼으로 software-only technique은 crypto-ransomware attack을 방해하기에는 적절하지 않다.
* 이런 문제를 해결하기위해서 우리는 hardware-assisted runtime crypto-ransomeware scheme, called Ranstop을 제안한다. 
* 주요 동기는 "HPC는 multi-dimentional hardware event-traces를 수집할 수 있고 micro-architecutral information을 프로그램이 실행되는 동안 hardware 수정없이 수집할 수 있다.
  * 장점:
    1. Being an integrated part of the hardware, HPCs operate transparently to any software running on the processor and collect targeted micro-architectural data irrespective to the program execution mode. Therefore, software-obfuscated or stealthy ransomware cannot evade them. (하드웨어의 통합된 부분인 HPC는 프로세서에서 실행되는 모든 소프트웨어에 투명하게 작동하고 프로그램 실행 모드에 관계없이 대상 마이크로 아키텍처 데이터를 수집합니다. 따라서 소프트웨어 난독화 또는 은밀한 랜섬웨어는 이를 회피할 수 없습니다.)
    2. It offers multi-dimensional information from a large set of micro-architectural sources. Therefore, acquired information can be utilized for multi-modal analysis techniques such as statistical analysis and machine learning techniques. (그것은 마이크로 아키텍처 소스의 큰 세트에서 다차원 정보를 제공합니다. 따라서 획득한 정보는 통계 분석 및 머신 러닝 기법과 같은 다중 모드 분석 기법에 활용할 수 있습니다.)
    3. Being an integrated part of the hardware, it is able to collect information significantly faster (often in us ranges) than any software-centric trace acquisition approach. (하드웨어의 통합된 부분이기 때문에 소프트웨어 중심 추적 수집 접근 방식보다 훨씬 더 빠르게(종종 우리 범위 내에서) 정보를 수집할 수 있습니다.)
    4. Developed ML model can be retained with additional dataset for emeraging ransomwares with little to no modification in the ML framework. Also the developed ML model size is significantly samller compared to the static signature-based database. (개발된 ML 모델은 ML 프레임워크에서 수정이 거의 또는 전혀 없이 새로운 랜섬웨어를 위한 추가 데이터 세트와 함께 유지될 수 있습니다. 또한 개발된 ML 모델 크기는 정적 서명 기반 데이터베이스에 비해 훨씬 작습니다.)

* crpyto-ransomware family는 disk encrypion goodware들의 encryption과 data movement같은 다양한 서부루틴과 특정 종속성을 보여준다. 그리고 정확하게 구분하기 어렵다.
* 하지만 RanStop 기술은 hardware activity signatures 를 확인하여 begnin과 Ransomware를 잘 구분할 수 있다.
* 다음의 그림과 같이 구성하며 ransomware와 goodware 둘다의 HPC Signitual를 수집하고 ML을 통해 학습시켜 두개를 구분할 수 있게 한다. <- Contribution
![image](https://user-images.githubusercontent.com/67637935/140282203-0af70867-9107-457c-a9a7-2a2f2a40c42e.png)


