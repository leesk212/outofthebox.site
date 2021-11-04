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


## Contribution
* RanStop offers an accurate and noise-free collection of hardware-domain micro-architectural activites (e.g., branch prediction success/miss, L2 cache miss )(etc, for ongoing program(command) executions for detecting malicious program(command) executions for detecting malicious event traces.) This dytnamic approach is applicable for both known an unknown Ransomware with minimal rutime an zero hardware overhead.

> RanStop은 탐지를 위한 악성 프로그램(명령) 실행을 탐지하기 위한 진행 중인 프로그램(명령) 실행을 위해 하드웨어 영역 마이크로 아키텍처 활동(예: 분기 예측 성공/실패, L2 캐시 미스)의 정확하고 노이즈 없는 컬렉션을 제공합니다. 악의적인 이벤트 추적을 위해서.) 이 동적 접근 방식은 최소한의 런타임과 zero 하드웨어 오버헤드로 알려진 알려지지 않은 랜섬웨어 모두에 적용할 수 있습니다.

* RanStop utilizes the state-of-the art ML techniques with intelligent feature selection scheme to accurately detect ransomware. We carray out extensive analysis for 80 crypto-ransomware using RNN architecture using LSTM and global average pooling methods. Our technique provides 97% prediction accuracy on average for selected hardware performance groups.

> RanStop은 랜섬웨어를 정확하게 탐지하기 위해 지능형 기능 선택 체계와 함께 최첨단 ML 기술을 활용합니다. 우리는 LSTM과 글로벌 평균 풀링 방법을 사용하는 RNN 아키텍처를 사용하여 80개의 크립토 랜섬웨어에 대한 광범위한 분석을 수행합니다. 우리의 기술은 선택된 하드웨어 성능 그룹에 대해 평균 97%의 예측 정확도를 제공합니다.

* RanStop offeres significantly early-detection for ransomware by analytzing the micro-architectual data collected for 20 timestamps each 100us apart from the start of the execution (2ms in total). This allows to stop the malicious execution at a very early stage and protects the system an files long before undergoing significant, if not none, damage and data loss.

> RanStop은 실행 시작(총 2ms)부터 100us 간격으로 20개의 타임스탬프에 대해 수집된 마이크로 아키텍처 데이터를 분석하여 랜섬웨어를 상당히 조기에 탐지합니다. 이를 통해 매우 초기 단계에서 악의적인 실행을 중지하고 심각한 손상 및 데이터 손실이 발생하기 훨씬 전에 시스템 파일을 보호할 수 있습니다.

## Preliminaries

### Ransomeware Detection: Prior work
* Prior work:
  * Kharrazet al.
  * Andronio et al.
  * Scaife et al.
  * Sgandurra et al.
  * Moussaileb et al.
  * Limitation: These approaches also suffer from different challenges, e.g., program and memory overhead for creating virtual users and enviorment, latency and computational overhead for user storage data analysis,etc
 * Our work: Our proposed technique soley relies on hardware-level micro-architectural information that remains unaffected in case of program obfuscation, stealthy execution and infection strength, and is readily available in modern processors requiring zero hardware overhead. (우리가 제안한 기술은 프로그램 난독화, 은밀한 실행 및 감염 강도의 경우 영향을 받지 않고 유지되는 하드웨어 수준 마이크로 아키텍처 정보에만 의존하며 하드웨어 오버헤드가 없는 최신 프로세서에서 쉽게 사용할 수 있습니다.)

### Micro-architectural Event Monitoring for Malware Detection
* likwid & perf
* Micro-architectural characteristics of both goodware and malware programs can be noisy due to the diffusion of multiple program executions within a given time window. (굿웨어 및 맬웨어 프로그램 모두의 마이크로 아키텍처 특성은 주어진 시간 창 내에서 여러 프로그램 실행의 확산으로 인해 잡음이 있을 수 있습니다.) --> 그렇기에 실행 추적의 간단한 관찰에 의해서는 maclicious program을 구분하고 특징 짓기 어렵다.
 * 이것을 막기 위해서 다양한 논문들이 있다 1~3
  1.
  2.
  3.
* Most of these approaches considered various classes of malware and rootkits into one class of malicious program and therefore, combined generic signature traces without emphasizing the intrinsic nature (and subsequent micro-architectural activites) of the malware itself. (이러한 접근 방식의 대부분은 다양한 클래스의 악성 코드와 루트킷을 하나의 악성 프로그램 클래스로 간주하므로 악성 프로그램 자체의 본질적인 특성(및 후속 마이크로 아키텍처 활동)을 강조하지 않고 일반 서명 추적을 결합했습니다.)

* 결과적으로 이러한 기술들은 많은 데이터의 수집을 필요하고, 많은 틀린 관계성들을 제공해야한다. 그래서 이것은 "crypto-ransomware"같은 schemes focusing specific families of malware detection에 적합하지 않는다.
* Alam et al.이 만든 것도 소수의 ransomware에서만 타당하고 확장성 정보를 제공하지 않는다.
* 반면에 (최종적으로) 우리가 제안하는 논문은, 2ms내에 탐지가 가능하다.

### Security Enhancement via Advanced Machine Learning Techniques.
* 가장 chanlleging한 것은 malware detection이 benign operation과 구분할 수 없고, false detection으로 이끌 수 있다는 점이다.
* 그러나 신중하게 구성된 기계 학습 기술은 이러한 이벤트를 학습하고 구별하여 더 높은 신뢰도로 이상을 식별할 수 있습니다.
  * Selecting high-fidelity micro-architectural features and events
  * Choosing efficient machine learning techniques for classification.
* Micro-architectural event traces from selected HPCs in a timeseries fashion.
* RNN using LSTM(timeseries specify) with GAP(Global Average Pooling) to reduce overfitting
