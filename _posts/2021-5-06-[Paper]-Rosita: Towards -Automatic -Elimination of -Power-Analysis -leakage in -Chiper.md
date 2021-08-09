---
tags: 🌟paper-review security-defense
toc: True
---
# Rosita
1. Uses an emulator to detect leakage due to unintended interatctions between values   
2. Rewrites the code to eliminate the leakage.

* A code rewrite engine that uses a leakage emulator which we amend to correctly emulate the micro-architecture of a target system.
> 대상 시스템의 마이크로 아키텍처를 올바르게 에뮬레이터로 수정하기 위해 리크 에뮬레이터를 사용하는 코드 재작성 엔진이다.
* AES, ChaCha, Xoodoo등을 적은 패널티로 protected mask하였다. 
## Uniformed distributed --> only proven secure attack 
* many chiper's implementations employ masking techniques that combine **intermediate values** with **randomly selected masks**.
* --> the mask being uniformed distributed
* To fix this leaks ``` repeatedly "Tweak the code until it stops leaking ```
> 반복적으로 누출이 멈출 때까지 코드를 수정한다.
* We have set out to explore if leakage emulators can be used for automatic elimination of side channel leakage from software implemenations
> 소프트웨어 구현에서 사이드 채널 누출을 자동으로 제거하기 위해 누출 에뮬레이터를 사용할 수 있는지 조사하기 위해 착수했습니다.

* Code rewrite program: "ROSITA" + Extended leakage emulator: "ELMO"

  * ```ROSITA``` 
    1. rule-driven code rewrite engine.
    2. uses output from ELMO*
    3. to select rewrite rules and apply them at leaky points
    4. 
    * incorporates rules to mitigate leakage arising from operand interactions, register reuse, rotation operation, and memory operations. 
    * > 피연산자 상호 작용, 레지스터 재사용, 회전 연산 및 메모리 작업에서 발생하는 누출을 완화하기 위한 규칙을 통합합니다
 
  * ```ELMO``` has undergone a major upgrade to ELMO* for two reasons:
    1. Only detect leakage between consecutive instructions --> Detact leakage between instructions that are further apart.
    2. Only instructions --> Identify the accurate leagkage model
    3. Modify the workflow in ELMO to perform repeatly
    * It had to be able to tell ROSITA the cause of the leakage
    * > ROSITA에게 leakage의 원인을 알려줄 수 있는 기능
    * We have added support by including the values that instructions store in various micro-architectural storage elements, which hold state that can leak information.
    * > 추가적인 micro-architectural storage elemnets에서 leakage에 대한 값을 포함함으로써 지원을 추가함  

# Contribution
1. Propose a framework for generating first-order leakage resilient implementations of masked cipher.
2. Design and implement systematic approaches for identifying leakage through microarchitectural storage elements. (ELMO ->ELMO*)
3. Develop ROSITA that rewrites leaking instructions and eliminate leakage.
4. USE ROSTIA --> Result: AES,ChaCha,Xoodoo

# Result
![image](https://user-images.githubusercontent.com/67637935/117383032-cf7cb880-af1a-11eb-9d3f-946234503ced.png)

![image](https://user-images.githubusercontent.com/67637935/117402476-6bb9b600-af41-11eb-9aac-f03d8eec1f88.png)


# Workflow
![image](https://user-images.githubusercontent.com/67637935/117382988-b70c9e00-af1a-11eb-90a2-6e78e3a40d5c.png)
