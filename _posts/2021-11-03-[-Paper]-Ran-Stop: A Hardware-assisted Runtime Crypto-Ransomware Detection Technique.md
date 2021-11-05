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

## Contribution
* RanStop offers an accurate and noise-free collection of hardware-domain micro-architectural activites (e.g., branch prediction success/miss, L2 cache miss )(etc, for ongoing program(command) executions for detecting malicious program(command) executions for detecting malicious event traces.) This dytnamic approach is applicable for both known an unknown Ransomware with minimal rutime an zero hardware overhead.

> RanStop은 탐지를 위한 악성 프로그램(명령) 실행을 탐지하기 위한 진행 중인 프로그램(명령) 실행을 위해 하드웨어 영역 마이크로 아키텍처 활동(예: 분기 예측 성공/실패, L2 캐시 미스)의 정확하고 노이즈 없는 컬렉션을 제공합니다. 악의적인 이벤트 추적을 위해서.) 이 동적 접근 방식은 최소한의 런타임과 zero 하드웨어 오버헤드로 알려진 알려지지 않은 랜섬웨어 모두에 적용할 수 있습니다.

* RanStop utilizes the state-of-the art ML techniques with intelligent feature selection scheme to accurately detect ransomware. We carray out extensive analysis for 80 crypto-ransomware using RNN architecture using LSTM and global average pooling methods. Our technique provides 97% prediction accuracy on average for selected hardware performance groups.

> RanStop은 랜섬웨어를 정확하게 탐지하기 위해 지능형 기능 선택 체계와 함께 최첨단 ML 기술을 활용합니다. 우리는 LSTM과 글로벌 평균 풀링 방법을 사용하는 RNN 아키텍처를 사용하여 80개의 크립토 랜섬웨어에 대한 광범위한 분석을 수행합니다. 우리의 기술은 선택된 하드웨어 성능 그룹에 대해 평균 97%의 예측 정확도를 제공합니다.

* RanStop offeres significantly early-detection for ransomware by analytzing the micro-architectual data collected for 20 timestamps each 100us apart from the start of the execution (2ms in total). This allows to stop the malicious execution at a very early stage and protects the system an files long before undergoing significant, if not none, damage and data loss.

> RanStop은 실행 시작(총 2ms)부터 100us 간격으로 20개의 타임스탬프에 대해 수집된 마이크로 아키텍처 데이터를 분석하여 랜섬웨어를 상당히 조기에 탐지합니다. 이를 통해 매우 초기 단계에서 악의적인 실행을 중지하고 심각한 손상 및 데이터 손실이 발생하기 훨씬 전에 시스템 파일을 보호할 수 있습니다.

# Preliminaries

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

# RANSTOP: A Hardware-Assited RUNTIME CRYPTO-RANSOMWARE DETECTOR
* We built our framework utilizing the key observations presented in Demm et al.

  * The semantics of a program (goodware or ransomware) do not change significantly over different variants of similar functionality and class. (프로그램의 의미(굿웨어 또는 랜섬웨어)는 유사한 기능 및 클래스의 다른 변종에 대해 크게 변경되지 않습니다.)
  * While accomplishing a particular task (benign or malicious), there exist subtasks that cannot be radically modified and should exhibit similar micro-architectural footprints. (특정 작업(양성 또는 악의적)을 수행하는 동안 근본적으로 수정할 수 없고 유사한 마이크로 아키텍처 발자국을 나타내야 하는 하위 작업이 있습니다.)
* 이러한 관측은, 적절한 최적화 작업을 진행해주면 malware와 benign-ware를 구분할 수 있다.  왜냐하면 여러 crypto-ransomware 변종들 사이에 공격행위의 유사성으로 인해 유사한 의미론적 특성이 상당 부분 존재하기 때문이다.
* 또한 랜섬웨어와 굿웨어에 대한 머신러닝을 통해 추가 조사받을 수 있는 다차원 서명을 수집할 수 있다.

## Overview
![image](https://user-images.githubusercontent.com/67637935/140282203-0af70867-9107-457c-a9a7-2a2f2a40c42e.png)
* Major steps:
  1. Program database creation
  2. micro-architecutral event monitoring and data collection in timeseries fashion
  3. LSTM-based predictive model generation(training) 
  4. Testing Validation Run-time Detection
### 1. Program database creation

#### One key
* The good-ware database should contain different familes of benign programs with various workload.(굿웨어 데이터베이스는 다양한 작업 부하를 가진 다양한 종류의 양성 프로그램을 포함해야 합니다.)
* Especially, one should also consider computationally intensive programs (e.g., disk encyption programs such as VeraCrypt) that perform legit but similar operations with respect to that of a crypto-ransomware.(특히, 크립토 랜섬웨어와 관련하여 합법적이지만 유사한 작업을 수행하는 계산 집약적 프로그램(예: VeraCrypt와 같은 디스크 암호화 프로그램)도 고려해야 합니다.)
* The motivation behind is to offer similar semantic characteristics to different sub-routines of the crypto-ransomware as well as generic user specific benign programs.(이면의 동기는 일반 사용자별 양성 프로그램뿐만 아니라 크립토 랜섬웨어의 다양한 하위 루틴에 유사한 의미론적 특성을 제공하는 것입니다.)
* (심지어)Additionally, the non-encrypion benign binaries provide resemblance to silent crypto-ransomware which does not start execution at the very first moment of infection but resort to stealthy operation in the background to other legit programs. (또한 암호화되지 않은 양성 바이너리는 감염의 맨 처음 순간에 실행을 시작하지 않고 백그라운드에서 다른 합법적인 프로그램에 대한 은밀한 작업에 의존하는 자동 크립토 랜섬웨어와 유사합니다.)

#### SOLVE
* TO make sure that the proposed framework is capable of identifying even the smallest differences and is not over/under-fitted due to noise. (제안된 프레임워크는 가장 작은 차이도 식별할 수 있으며 노이즈로 인해 과대/과소 적합하지 않습니다.)
* For example, as experimented in Alam et al.(RATAFIA,RAPPER), a one-class classifier trained with random benign programs may tend to separate crypto-ransomware more accurately from a text editor program, but may not distinguish from a disc encryption or file zipping program. (예를 들어 Alam et al.(RATAFIA,RAPPER)에서 실험한 바와 같이 임의의 양성 프로그램으로 훈련된 1등급 분류기는 크립토 랜섬웨어를 텍스트 편집기 프로그램과 더 정확하게 분리하는 경향이 있지만 디스크 암호화 또는 파일 압축 프로그램은 구분하지 못한다.)
* Therefore we adopt a well-balanced database with significant number and variants of ransomware and goodware. (따라서 상당한 수와 변종 랜섬웨어 및 굿웨어가 포함된 균형 잡힌 데이터베이스를 채택합니다.)
* This well-balacned training scheme allows to reduce false postive and false negative by the classifier. (이 잘 균형 잡힌 훈련 계획은 분류기에 의해 거짓 긍정 및 거짓 부정을 줄일 수 있습니다.)
* Note that the Ranstop framework is readily scalable to a larger dataset, as we discuss in Section 4 and it allows the user to re-train(update) the initial model for finer detection with emerging threats. (Ranstop 프레임워크는 섹션 4에서 논의한 것처럼 더 큰 데이터 세트로 쉽게 확장할 수 있으며 사용자가 새로운 위협에 대한 더 정밀한 탐지를 위해 초기 모델을 다시 훈련(업데이트)할 수 있습니다)

### 2. Micro-architectural Event Monitoring and Data Collection
* Linux-OS에서 VM (Window OS)
  * It averts the risk of crpyto-ransomware encrypting the collected HPC data which is stored in a seperate administrative-privileged directory; (별도의 관리 권한이 있는 디렉토리에 저장된 수집된 HPC 데이터를 암호화하는 crpyto-ransomware의 위험을 방지합니다.)
  * The Linux system provided inhospitable environmnet in case any ransomware binary manages to escape the Windows VM, so that the rest of the networked system (if any) in the experimental setup is unaffected. (Linux 시스템은 랜섬웨어 바이너리가 Windows VM을 탈출하는 경우에 대비하여 열악한 환경을 제공하여 실험 설정의 나머지 네트워크 시스템(있는 경우)은 영향을 받지 않습니다.)
* The crypto-ransomware and benign programs from the database are executed in a random fashion to monitor and collect micro-architectural information from the processor using HPCs.(데이터베이스의 크립토 랜섬웨어 및 무해한 프로그램이 무작위로 실행되어 HPC를 사용하여 프로세서에서 마이크로 아키텍처 정보를 모니터링하고 수집합니다.) using likwid
* To make sure that the virtual machine offers the same workload signature with or without infection, we collected the HPC data in the following fashion. (가상 머신이 감염 여부에 관계없이 동일한 워크로드 서명을 제공하는지 확인하기 위해 다음과 같은 방식으로 HPC 데이터를 수집했습니다.)

![image](https://user-images.githubusercontent.com/67637935/140311838-1fe4c232-7d71-4e58-b7f0-67d11c0e1248.png)
  * The Window VM is hosted and run with a complete library of programs to replicate a real-life workload. (Window VM은 실제 워크로드를 복제하기 위해 완전한 프로그램 라이브러리와 함께 호스팅 및 실행됩니다.)
  * The program under test (ransomware or goodware) is pinned to run inside the VM with no thread/resource limitations (테스트 중인 프로그램(랜섬웨어 또는 굿웨어)은 스레드/리소스 제한 없이 VM 내부에서 실행되도록 고정됩니다.)
  * Likwid is used to collect and store timestamp data from all the CPU cores in the Linux host machine. (Likwid는 Linux 호스트 시스템의 모든 CPU 코어에서 타임스탬프 데이터를 수집하고 저장하는 데 사용됩니다.)
  * Once the targeted timeseries data is collected (e.g., by completion of the program or timeout); The VM is destroyed along with its virtual storage completely wiped to reduce any residual noise. (대상 시계열 데이터가 수집되면(예: 프로그램 완료 또는 타임아웃) 가상 저장소와 함께 VM이 완전히 삭제되어 잔류 소음이 줄어듭니다.)
  * A new VM replaces the old one (e.g., corrupted one, if infected by ransomware while collecting ransomware data) with a backup storage image hjaving the same state as prior to running the program. (새 VM은 기존 VM(랜섬웨어 데이터 수집 중 랜섬웨어 감염 시 손상된 VM)을 프로그램 실행 전과 동일한 상태의 백업 스토리지 이미지로 교체합니다.)
  * Multiple iterations are performed to collect all possible micro-architectural events with randomized execution order, so that there exists no systematic data and memory collection, irrespective to ransomware or goodware execution. (랜섬웨어나 굿웨어 실행에 관계없이 체계적인 데이터 및 메모리 수집이 존재하지 않도록 임의의 실행 순서로 가능한 모든 마이크로 아키텍처 이벤트를 수집하기 위해 여러 번 반복 수행됩니다.)
#### Collected HPC data Table
![image](https://user-images.githubusercontent.com/67637935/140323263-0ba7427c-5976-4c4f-a8a3-4a62231236e6.png)
* This associated events are given inputs to the next stage LSTM network for ML-based predictive model building. (이 관련 이벤트는 ML 기반 예측 모델 구축을 위한 다음 단계 LSTM 네트워크에 대한 입력이 제공됩니다)

### 3. LSTM-based Predictive Model Generation (training and validation)
![image](https://user-images.githubusercontent.com/67637935/140324167-21f7cebe-b88f-440d-9ff5-3bd9643febe6.png)
1. LSTM Layer
2. GPA Layer is used to reduce intrinsic training features created by the LSTM Layer. (Down Overfitting)(GPA 계층은 LSTM 계층에서 생성된 고유한 훈련 기능을 줄이는 데 사용됩니다.)

### 4. Model Generation, Validation and Detection
* It should be noted that the impacts of different optimizers and the micro-architectural performance groups collected in previous steps are not all same for detecting potential crypto-ransomware with high accuracy. (다양한 옵티마이저와 이전 단계에서 수집한 마이크로 아키텍처 성능 그룹의 영향이 잠재적인 크립토 랜섬웨어를 높은 정확도로 탐지하는 데 모두 동일한 것은 아니라는 점에 유의해야 합니다.)
* Also since the number of HPC are limited on any system, the real time detection program (watchdog) can only be trained to work for certain performance groups and may not swap between monitors to monitor different set of data too often.(또한 모든 시스템에서 HPC의 수가 제한되어 있기 때문에 실시간 감지 프로그램(워치독)은 특정 성능 그룹에 대해서만 작동하도록 훈련될 수 있으며 다른 데이터 세트를 너무 자주 모니터링하기 위해 모니터 간에 교환하지 않을 수 있습니다.)

## Experimental Results

### Platform
* Xeon CPU , Coffeelake
* Ubuntu 16.04, Likwid

### Program Database Creation
* 80 crypto-ransomware (from VirusShare, .exe files) & 76 benign (goodware) program (from OpenSSL and collection of random C programs from Github, govdocsl)
* Execution time of at least 2ms or more (only 2ms because our primary objective is an ealry detection)

### Micro-architectural Event Capture
* HPC information was collected for 20 timestamps, each being 100us apart --> 20X100us --> 2ms --> 충분히 공격을 탐지하기에 일찍이다. 
* We also saw that the timeseries data differences between the two classes were not necessarily significantly large to readily distinguish between ransomware versus goodware. (또한 랜섬웨어와 굿웨어를 쉽게 구별할 수 있을 정도로 두 클래스 간의 시계열 데이터 차이가 크게 크지는 않다는 것도 확인했습니다.)
* Additionally, the differences (or similarities) at some timestamps might have occurred due to system noise and additional runtime overhead. Therefore, it was necessary that the developed detection scheme ccould reduce any noise and optimize the intrinsic features to accurately identify ransomware threats. (또한 시스템 노이즈 및 추가 런타임 오버헤드로 인해 일부 타임스탬프에서 차이점(또는 유사점)이 발생할 수 있습니다. 따라서 개발된 탐지 체계는 랜섬웨어 위협을 정확하게 식별하기 위해 노이즈를 줄이고 고유 기능을 최적화할 수 있어야 했습니다.)
* It should be noted that each performance group can collect 4 or fewer micro-architectural events due to hardware limitations. Because, for our experimental system, only four general-purpose HPCs are available in each core when hyperthreading is enabled [14]. (각 성능 그룹은 하드웨어 제한으로 인해 4개 이하의 마이크로 아키텍처 이벤트를 수집할 수 있습니다. 실험 시스템의 경우 하이퍼스레딩이 활성화된 경우 각 코어에서 4개의 범용 HPC만 사용할 수 있기 때문입니다[14].)
* In addition to micro-architectural event count, likwid readily provides scalar information based on different performance metrics as shown in Table 1. (마이크로 아키텍처 이벤트 수 외에도 likwid는 표 1과 같이 다양한 성능 메트릭을 기반으로 스칼라 정보를 쉽게 제공합니다.)
* 분석을 쉽하게 하기 위해서 우리는 사용가능한 pre-processed metric 정보를 생각했다. (연속되는 단계에서 feature를 선택하고 훈련시키고 테스팅하기 위해서). (noisy가 있거나 scaling과 alignment 같은 data-preprocessing을 요구하는 원시 hardware event counts보다는..)

### Performance Analysis of the ML Classifier
* Selecting such groups and features depend on multiple factor
 1. inherent properties of the ML technique that utilizes such features to prerfrom binary classification (이러한 기능을 활용하여 이진 분류를 수행하는 ML 기술의 고유한 속성)
 2. the program behavior that is running on the system (performing extensive encryption versus simple output prinitng) (시스템에서 실행 중인 프로그램 동작(단순한 출력 인쇄에 비해 광범위한 암호화 수행))
* Equal distribution of benign and ransomware was maintained in the training dataset to prevent inclination of ML towards a specific dataset. (특정 데이터 세트에 대한 머신러닝의 성향을 방지하기 위해 훈련 데이터 세트에서 양성 및 랜섬웨어의 균등한 분포를 유지했습니다.)
* The training was done on four different optimizers belonging to different classes, i.e., SGD, Adamax, Adadelta, and RMSprop, to calibrate network weights based on error for reducing the validation loss (검증 손실을 줄이기 위해 오차를 기반으로 네트워크 가중치를 보정하기 위해 SGD, Adamax, Adadelta 및 RMSprop와 같은 서로 다른 클래스에 속하는 4개의 서로 다른 옵티마이저에 대해 교육을 수행했습니다.)
* We used 25% of the training dataset for validation after each epoch to efficiently calibrate the loss function of the model (모델의 손실 함수를 효율적으로 보정하기 위해 각 에포크 후 검증을 위해 훈련 데이터 세트의 25%를 사용했습니다.)
* Also to reduce any bias in the model due to misfitting, the accuracy analysis was performed over 50 iterations, where each run contained randomly shuffled executables and trained for 1000 epoch. (또한 부적합으로 인한 모델의 편향을 줄이기 위해 정확도 분석이 50회 반복 수행되었으며 각 실행에는 무작위로 섞인 실행 파일이 포함되어 있고 1000 에포크 동안 훈련되었습니다.)
* For an in-depth analysis of the RanStop technique, we analyzed the accuracy of the predictive model where it was developed using different sizes of training dataset, namely 70% (Table 2), 80% (Table 3), and 90% (Table 4) with previously mentioned optimizers. Here, each value represents the fraction of the total dataset that was used for training. (RanStop 기술의 심층 분석을 위해서 우리는 예측있는 모델의 정확도를 분석했다. (training dataset의 다른 크기로 사용하면서: 70%, Table2... 다양한 Optimizer들과 함께). 여기에는, 각각 값들은 훈련을 위해 사용된 전체 데이터 셋의 일부를 대표한다. 즉, 70%/30%(Train,Test) ... 이런 방식으로!

#### HPC with Table
![image](https://user-images.githubusercontent.com/67637935/140471252-87af37f2-a18f-43a0-a6b8-1243ebc723bb.png)
![image](https://user-images.githubusercontent.com/67637935/140471221-85ac8521-89db-4854-96ba-52cb73decc33.png)
![image](https://user-images.githubusercontent.com/67637935/140471193-156dbc8c-1909-45da-98cb-72c68945f920.png)

* For each table, the detection accuracy (averaged over 50 iterations) is listed with the HPC groups (row) and optimizers (column). (각 표에 대해 탐지 정확도(50회 반복에 대한 평균)가 HPC 그룹(행) 및 최적화 프로그램(열)과 함께 나열됩니다.)

#### Table 분석 결과
* Optimizer 
  * Best: Adaelta / Worst: SGD 
  * Scrutiny: the SGD overfitted the model due to the lack of data endpoint (SGD는 데이터 끝점의 부족으로 인해 모델을 과적합했습니다.)
* HPC Feature
  * Best: TLB_DATA
  * Worst: TLB_INSTR
  * 70/30 --> 96% Accuracy
  * 90/10 --> 97% Accuracy
* We also note that the programs (ransomware/benign) used for testing the model were not any part of the training dataset, as mentioned previously. Therefore, this supervised classifier is fully compatible for detecting unknown ransomware, i.e., emerging variants with no (or, very limited, if needed at all) retraining. (또한 모델 테스트에 사용된 프로그램(랜섬웨어/양성)은 이전에 언급한 것처럼 훈련 데이터 세트의 일부가 아닙니다. 따라서 이 감독 분류기는 알려지지 않은 랜섬웨어, 즉 재교육이 없는(또는 필요한 경우 매우 제한적인) 새로운 변종을 탐지하는 데 완벽하게 호환됩니다.)

#### FP,FN 분석
* We consider both false negatives and false
positives as major drawbacks for any ransomware (or malware, in general) detection technique, since false positives, i.e., true benign programs deemed as ransomware, cause inconvenience and probable denial of service, whereas false negatives, i.e. true ransomware detected as benign program, can cause catastrophic damage to the system. (우리는 거짓 부정과 거짓을 모두 고려합니다. 모든 랜섬웨어(또는 일반적으로 맬웨어) 탐지 기술의 주요 단점으로 긍정적인 점은 거짓 긍정, 즉 랜섬웨어로 간주되는 진정한 양성 프로그램은 불편을 초래하고 서비스 거부 가능성이 있는 반면, 거짓 부정, 즉 양성 프로그램으로 탐지된 진정한 랜섬웨어는 시스템에 치명적인 손상을 줄 수 있습니다.)
* 결과는 50번을 평균낸 것이다.
* 결과는 보여준다. 만약 우리가 TLB_INSTR을 제거한다면 FR 비율(Crypto-ransomware를 begnign으로 판단하는 비율)은 1% 적어진다. 
* The result also shows the false positive (identifying benign as ransomware) is little high that can be concluded to the fact that many micro-architectural activities of benign programs may resemble that of a crypto-ransomware. We expect that the false positive will significantly reduce with the increase in the dataset size and diversity as a future work. (356 / 5000
번역 결과
결과는 또한 양성 프로그램의 많은 마이크로 아키텍처 활동이 크립토 랜섬웨어의 활동과 유사할 수 있다는 사실로 결론지을 수 있는 오탐(양성을 랜섬웨어로 식별)이 거의 높지 않음을 보여줍니다. 향후 작업으로 데이터 세트의 크기와 다양성이 증가함에 따라 가양성(false positive)이 크게 줄어들 것으로 기대합니다.)
![image](https://user-images.githubusercontent.com/67637935/140474104-3d977ae8-f7a1-4097-9388-adaa48a9fb4e.png)
![image](https://user-images.githubusercontent.com/67637935/140474119-2d7e857e-30aa-49ed-bc68-03c8688bec77.png)

### Benchmarking
![image](https://user-images.githubusercontent.com/67637935/140474180-d5b3db60-11ad-4b40-bc29-73e9d33d83f5.png)
* In Table 7, we provide a comparative analysis between our proposed RanStop scheme and existing state-of-the-art techniques for ransomware detection. The table lists different detection techniques, dataset sizes, and performance metric. As it is shown, RanStop has provided significiantly better result over a comprehensive database of ransomware and goodware; and can provide an early detection with very high accuracy. (표 7에서는 제안된 RanStop 방식과 기존의 랜섬웨어 탐지 기술을 비교 분석했습니다. 표에는 다양한 탐지 기술, 데이터 세트 크기 및 성능 메트릭이 나열되어 있습니다. 표시된 대로 RanStop은 랜섬웨어 및 굿웨어의 포괄적인 데이터베이스보다 훨씬 더 나은 결과를 제공했습니다. 매우 높은 정확도로 조기 발견을 제공할 수 있습니다.)


