# ABSTRACT
```
Abstract—The growing security threat of microarchitectural
attacks underlines the importance of robust security sensors and
detection mechanisms at the hardware level. While there are
studies on runtime detection of cache attacks, a generic model to
consider the broad range of existing and future attacks is missing.
Unfortunately, previous approaches only consider either a single
attack variant, e.g. Prime+Probe, or specific victim applications
such as cryptographic implementations. Furthermore, the state-
of-the art anomaly detection methods are based on coarse-grained
statistical models, which are not successful to detect anomalies
in a large-scale real world systems.
Thanks to the memory capability of advanced Recurrent
Neural Networks (RNNs) algorithms, both short and long term
dependencies can be learned more accurately. Therefore, we
propose FortuneTeller, which for the first time leverages the
superiority of RNNs to learn complex execution patterns and
detects unseen microarchitectural attacks in real world systems.
FortuneTeller models benign workload pattern from a microar-
chitectural standpoint in an unsupervised fashion, and then,
it predicts how upcoming benign executions are supposed to
behave. Potential attacks and malicious behaviors will be detected
automatically, when there is a discrepancy between the predicted
execution pattern and the runtime observation.
We implement FortuneTeller based on the available hardware
performance counters on Intel processors and it is trained with 10
million samples obtained from benign applications. For the first
time, the latest attacks such as Meltdown, Spectre, Rowhammer
and Zombieload are detected with one trained model and without
observing these attacks during the training. We show that
FortuneTeller achieves the best false positive and false negative
trade off compared to existing works under realistic workloads
and target implementations with the highest F-score of 0.9970.
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
