# Abstract

```
Abstract—
The growing security threat of microarchitectural attacks underlines the importance of robust security sensors and detection mechanisms at the hardware level. While there are studies on runtime detection of cache attacks, a generic model to consider the broad range of existing and future attacks is missing.Unfortunately, previous approaches only consider either a singleattack variant, e.g. Prime+Probe, or specific victim applications such as cryptographic implementations. Furthermore, the state-of-the art anomaly detection methods are based on coarse-grained
statistical models, which are not successful to detect anomalies in a large-scale real world systems. Thanks to the memory capability of advanced Recurrent Neural Networks (RNNs) algorithms, both short and long term dependencies can be learned more accurately. Therefore, we
propose FortuneTeller, which for the first time leverages the superiority of RNNs to learn complex execution patterns and detects unseen microarchitectural attacks in real world systems. FortuneTeller models benign workload pattern from a microar-chitectural standpoint in an unsupervised fashion, and then, it predicts how upcoming benign executions are supposed to behave. Potential attacks and malicious behaviors will be detected automatically, when there is a discrepancy between the predicted execution pattern and the runtime observation. We implement FortuneTeller based on the available hardware performance counters on Intel processors and it is trained with 10 million samples obtained from benign applications. For the first time, the latest attacks such as Meltdown, Spectre, Rowhammer and Zombieload are detected with one trained model and without observing these attacks during the training. We show that FortuneTeller achieves the best false positive and false negative trade off compared to existing works under realistic workloads and target implementations with the highest F-score of 0.9970.
```

> * 증가되는 mircoachitectural attacks 에 대한 보안 위험은 "세밀한 보안 센서"와 "탐지 메카니즘"의 중요성을 강조한다.  
> * 일반적인 모델인 "The broad range of existing"과 "future attack"에 관한 연구가 부족하다.   
> * 이전의 접근은 단순하게 "single attack variant(eg. **Prime+Probe**)" 또는 "암호적 침입"같은 것에 대한 접근만 있었다.  
> * 최신의 이상 탐지는 "Coarse grained stastical models"에만 기반되어있고 이것은 성공적으로 실제 세계의 큰 시스템을 모두 탐지하지 못한다.  
>  
> * "RNN"모델 덕분에 짧고 긴 기간 의존성들은 더욱 정확하게 학습될 수 있다.
> * "FortuneTeller"는 RNN의 특정 이점을 사용하고, 실제 세계의 보이지 않는 microachitectual attack들을 탐지한다.
> * "FortuneTeller"는 "비지도된 fashion"에서 "ma standpoint"에서 "benign한 workload pattern"을 모델링한다. 
> * "FortuneTeller"는 실핼될 benign 실핼들이 어떻게 행동될지를 예측한다.  
> * 예측 실행 패턴과 실행 관찰사이의 불일치가 있을때, Potential Attack과 이상 행동은 자동적으로 탐지되어진다.  
>  
> * "FortuneTeller"는 Intel Process에 기반한 h/w performance counter에 기반하였고, 이것은 benign application으로부터 만들어진 10만개의 sample들을 이용하였다.  
> * Meltdown, Spectre,Rowhammer,Zombieload들을 1 trained model에서 탐지했고 훈련동안은 이러한 공격들의 관찰되는 것이 없다는 것이 탐지되었다.  
> * FortuneTeller가 최상의 오탐과 최저의 오탐을 달성했다고 생각한다. 약 F-0.9970

# Instruction
## Spectre and Meltdown attack 
Allow a user with minimum access right to easily read arbitraty locations in the memory by exploiting the transient effect of illegal instruction sequences

## How can we discover **dormant vulnerabilites** and protect against such **subtle attacks**?

A fundamental approach is to eliminate the leakage altogether by using formal analysis.  
However, given H/W 발전되면 근 미래에 찾는 것은 비현실적이다.   
그렇기에 남은 방법은 운영 방법을 바꾸는 것이다.
leaks are patched as they are discovered by researchers through inspection and statistical analysis.
  
**Microarchitectural side-channel attacks**는 그렇기에 os단에서 보안 강화(hardening),software synthesis, analysis, static or dynamic detection of attacks를 한다. 

**Static analysis** is performed by evaluating the untrusted sw against known malicious code patterns **without running it on a target platform.**  

**Dynamic analysis** aims to detect malicious behavior in the system by analyzing **the runtime footprint of the running process**  

microarchitectural attacks의 동적 탐지에서 현재 존재하는 일들은 h/w performance counter로부터 collecting footprints에 기반 되어있고 악성행위의 모델링을 제한하는 것에 기반되어있다.  

중요한 문제점들 목표는 탐지기술, 은 부족이다. 정보의 새로운 공격 백터들의 

그래서 이상행위를 모델링하는 것은 목표는 발견되어지지 안흔ㄴ 공격그리고 정확하게 그들을 구분하는 것|출발지는 정상 행동|은 계속된 문제이다.

그래서 microarchitectural attacks는 시작단계이고 지도된 learnong모델들이고 이것들은 사용되어진다. 공격 분류기로 그리고 의존하지않는다 |나아가서 탐지지는 것을| 알려진 공격들을| 불충분한 양때문에 그리고 중요하지않는 라벨링의 데이터들 떄문에 

그림으로 비지도학습 방법은 더 약속되어진다. 적용하는 것을 탐지 모델들을 실제 세계의 시나리오들에게

이상에 기반된 공격 탐지들은 또한 연구되어진다 다른 보안 기능들에게서 목표로한다. 나아가서 앞서 언급한 문제들을 표현하기 위해서 힘들받는 것은 온화한 행동과 outlier을 탐지하는 것에서 

**Cache attacks**의 기반된 이상 탐지 노력이 있는 동안 현대 m,a는 **side-channel attakcs**로부터 공격을 겪고 있다. 

그럼으로 detection techniques는 실용적이고 사용적이지 않다. 만약 그들이 전체 범위를 커버하지 아는다면 알거나 보이지않는 공격들의. 

이것은 요구한다. 더 향상된 배우는 알고리즘들을 나아가서 모델들을 이해하기 위해서 전체 행위 본질은 m,a

반면에 통계적인 모델 (목표는 이상 탐지)는 충분하지 않다. 나아가서 분석하는 것에, 수백만개의 이벤트들을 현대 ma에서 생성된 복잡한 시스템들의

주요한 한계, **classical stastictical learning metho**의, 는 그들은 한손 설정된 특징을 사용한다. 그리고 이것은 낭비한다. 주요한 정보들을 나아가서 특정화하는데 온하한 프로그램을 패턴화하는데. 결국에 이 기술들은 실패했다. 실제 세계에서 generic model들을 사용하는 것에.

**RNN** 과 **LSTM**과 **GRU** network를 사용하여 기존의 sequential flow들을 잘 파악하지 못했던 현대 modernachitecture들을 파악할 수 있게 모델링을 하였다. 

## Contribution
* Propose Fortuneteller  
 * first generic detection model/technique for microarchitectual attacks  
 * h/w,s/w의 정상적인 행동을 학습하고 (ma의 이벤트 관찰로 부터 얻어진) 이상 행동으로서 훈련된 데이터들을 형성하지 않는 outlier들을 분류한다.  
 * 보이지않는 m,a attack을 탐지할 수 있다.   

# Section2: provide background imformation about RNN and microarchitectual attacks
* A. Microarchitectural Attacks
  컴퓨터의 기능 향상으로 엄청나게 복잡하고 최적화되게 computer archetecture가 구현되었다. 
  성능을 향상시키기 위해서 몇몇의 low-level의 특징들이 소개되어진다.  
  "1. speculative branch /2. out-of-order executions /3. shared LLC(Last level cache)"  
  이 3가지 모두다 m,a Attack의 공격 요소이며 Fortuneteller는 m,a를 공격하는 아래 3가지 기법들을 탐지할 수 있다.
 * Flush+Reload:
  
  
