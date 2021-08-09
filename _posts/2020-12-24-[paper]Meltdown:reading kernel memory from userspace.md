---
tags: 🌟paper-review security-attack csca
toc: True
---
# Abstract

# Introduction

# Background

## Out of oreder excution
* In practice, CPUs supporting out-of-order execution allow running operations **speculatively** to the extent that the processor's out-of-order logic processess instructions before the CPU is certain that the instruction will be needed and committed.
* In this paper, we refer to speculative execution in a more restricted meaning, where it refers to an instruction sequence following a branch, and use the term out of order execution to refer to any way of getting an operation executed before the processor has committed the results of all prior instructions.
* For dynamic scheduling of instructions to allow out-of-order execution
  * A unified reservation station allows **a CPU to use a data value** as it has been *computed* instead of *sorting* it in a register and *re-reading* it.
  * The reservation station renames registers to allow instructions that operate on the same physical registers to use the last logical one to solve read-after-write, WAR, WAW hazards 
  > MULTD F4,F2,F2  
  > ADDD F2,F0,F6(X)  
  > ADDD F8,F0,F6(Dependency on F2 don't occur)
* 슈퍼 스칼라와 같이 CPU 내에 파이프 라인에 4개에서~5개 되는 명령어들이 같이 패치 되게된다.  
  * 그리고 그 명령어들은 마이크로옵스에 의해 디코딩 되어지고 Reorder Buffer로 복사가 되어진다.
* 이때 Reorder Buffer에서 의존성이 있는 레지스터들을 renaming 해주는 등의 과정을 진행해주어 in-order에서 out-of-order로 최적화된 명령어 수행이 된다. 그리고 queue(Unified Reservation Station)에 들어가게 될때 원래의 순서에 맞게 정렬이 되어진다.

### Speculative excution
* 이는 out-of-order와 다른 excution이다. 
> IF( CONDITION ){ } ELSE { }  

 에서, 추측실행이란 CONDITION이 맞다는 가정하에 CONDITION안의 BODY를 실행시키는 명령어이다.
* Out of order excution과 구분이 필요하다.

## Cache Attacks

# A Toy Example
> raise_exception();  
> access(probe_array[data * 4096])  

* 이 과정에서 집중해야하는 것은 data의 * 4096번지를 곱한 것이다. 그 이유는 flush+reload에서 아이디어를 얻을 수 있는 것과 마찬가지로, prefetch때문이다. flush+reload에서 로드하는 과정에서 페이지 단위로 로드를 하기 때문에 정확히 어느 곳의 접근을 했는지에 대해서 파악할 수 없다. 그렇기에 그것을 막아주고자 data에 4096을 곱하여 원하는 커널 주소(데이터)의 값을 파악하기 용이하게 만들 수 있다. 
* 첫번째 라인에서 원래대로라면 EXCEPTION이 발동되어, 두번째 명령어가 실행이 되면 안된다. 
* 하지만 슈퍼스칼라에 의해서 두가지의 명령어가 같이 페치되어, Reorder Buffer에서 두개의 명령어다 실행이 가능한 상태로 메모리 접근을 한다.
* 그렇게 되면 이미 접근한 메모리에 대한 페이지들이 캐시에 올라와있게 되고, 실행된 probe_array의 모든 페이지들을 Flush+Reload를 진행하게되면 data에 특정 페이지에 대한 접근만 빠르게 됨으로 어느 페이지가 물리 메모리에 접근 되었는지를 확인할 수 있게된다. 
* access와 같이 일시적으로 존재되는 명령어들을 transient instruction이라 부른다.
* 비밀 값을 유출시키기 위해서는 transient instruction이 공격자가 유출시키기 원하는 비밀 값을 이용하고 있어야만한다.


# Building Blocks of the Attack
![meltdown_block](../assets/images/block.png)
* Meltdown 구현의 첫번째 block에서는 transient instruction의 실행이 있고, CPU가 경험적 지속시간을 최적화 시키기위해서 계속적으로 현재 명령어 앞에서 실행되고 있기 떄문에 모든 순간에 일어난다.
* Transient Instuction은 만약 그들의 operation이 secret value를 갖고 있다면 side channel attack을 실행시킬 수 있다.
* userspace에서 kernel space를 접근하는 주소값이여야 하고, kernel space인 이유는 kernel address space에서는 모든 physical memory로의 접근이 가능하기 떄문이다.
* 하지만 userspace에서 kernelspace를 접근하는 것은 예외처리를 발생시키고 바로 어플리케이션이 종료되도록 만들어버린다. 그렇기에 예외처리가 발생되더라도 그것을 다룰 수 있는 과정이 있어야만 공격자는 Secret value를 누출시킬 수 있다.

## Excuting Transient Instructions
* 원래대로라면 exception이 발동되었을때 바로 꺼져야 하는데 바로 꺼지지 않게 어떻게 할까?
1. Exception handling: 예외처리가 일어난 후를 효과적으로 다루는 방법
* 유효하지 않는 메모리 주소값을 접근하기 전에 공격하는 중인 application을 fork하는 방법이다. 그리고 child process에서만 유효하지 않는 메모리 위치로 접근하는 것이다. 
  * CPU는 transient instuction sequence를 child process에서 충돌나기전에 실행한다.
  * Parent Porcess는 Microarchitectural state(side-channel)를 관찰함으로써 secret value들을 복구할 수 있다. 
* 특정한 예외처리가 일어났을 때 시행되는 Signal handler를 설치하는 것으로도 가능하다. 
  * 공격자가 명령어들의 sequence를 발행하도록 하고, 그리고 crash로부터 어플리케이션을 막음으로써 새로운 프로세스를 생성하지 않기 때문에 overhead를 줄일 수 있다. 
2. Exception suppression: 예외처리가 일어나는 것을 막고 그리고 control flow를 redirect하는 방법
* 처음 문제가 제기되는 것에서부터 예외처리 후 꺼지는 것을 막아버리는 방법
* Transaction memory는 메모리 Access를 하나의 원자 작동으로 사용할 수 있게 그룹화 해주어, 에러가 발생한다면 그 이전의 상태로 돌아갈 수 있도록 option을 제공해준다.
* 만약 예외처리가 Transaction에서 발생된다면, architectural state는 초기화되고, 그리고 프로그램 실행은 방해없이 지속된다.
* Speculative execution은 branch misprediction때문에 실행된 코드 path에서 실행되지 않을 것 같은 명령어를 발생시킨다.
* 이전 조건 branch를 처리하는 것에 따르는 이러한 명령어들은 추측적으로 실행된다.
* 그럼으로, 유효하지 않은 메모리의 접근은 (단지 이전의 branch condition이 true이기에 실행이 되었던) 추측명령어들에 의해서 실행되어진다.
* condition들이 실행 코드에서 결코 true라고 평가하지 않을 것이라 확신이들도록 만듦으로써 우리는 일어나는 예외들(메모리 접근이 단지 추측적이게 실행되어지는)을 억압시킬 수 있다. 

## Building a Covert Channel
* 두번째 Meltdown block을 보게되면 transient instruction이 마직막에는 microarchitectual covert channel을 통해 정보를 보내고 있는 것을 확인할 수 있다.
* covert channel의 끝에서 받는 것은 microarchitectural의 변화를 받는 것이고 state로부터 secret을 추정할 수 있게 하는 것이다.
* 받는 곳은 transient sequence의 부분이 아님을 주목하고 다른 쓰레드, 또는 프로세스가 될 수 있다. (예를들어 fork에서 parents process)
* 우리들은 Cache attack으로부터 기술들을 영향력있게 한다. 왜냐하면 cache state는 다양한 기술들을 사용해서 architectual state를 믿을 수 있게 전송되어질 수 있는 microarchitectural state이기 때문이다.
* 특히 우리는 F+R을 사용하고, secret value에 따라서 transient instruction sequence는 regular memory access를 시행한다. 
* trasient instruction sequence가 접근할 수 있는 페이지에 접근한 후에(covert channel의 sender의 실행한 후에), 주소는 subsequent access를 위해 cached 되어져있다.
* receiver는 이것을 감시할 수 있다. 주소가 cache에 load되었는지 안되었는지를, 주소의 access time을 측정함으로써(FR의 개념)
* 그럼으로 sender는 주소에 접근함으로써 1을 보낼 수 있고 주소에 접근하지 않음으로써 0을 보낼 수 있다.
- in toy example
  * 다수의 다양한 종류의 cache line들을 사용함으로써 한번에 multibit을 전송할 수 있다.
  * 모든 256 different byte value를 위해 sender는 다른 cache line을 접근할 수 있다.
  * 256 가능한 모든 cache라인들에 Flush+Reload 공격을 진행함으로써 receiver는 한비트가 아닌 모든 비트를 복구할 수 있다.
  * 하지만 F+R 공격은 매우 길기 때문에, 한번에 한비트만 보내는 것이 더 효과적이다.
  * 공격자는 간단하게 secret value를 변조하고 변화 시킬 수 있다. 
- 다른 instruction을 갖고 covert channel 만들기
  * covert channel은 단지 cache에 의존되어진 microachitectural에 제한되어 있지않다.
  * 모든 mircroarchitectual state는 instruction에의해 영향받아질 수 있고 side channel(covert channel을 만들 수 있는)을 통해 관측되어질 수 있다. 
  * 예를들어 1bit을 보내기 위해서 ALU와 같은 실행 port(?)를 점령함으로써 covert channel을 만들 수 있다. 
  * receiver는 같은 명령어 port에서 instruction을 실행할 때 latency를 측정할 수 있다. 높은 latency는 1bit를 sender가 보냈음을 말해주고, 낮은 latency는 0을 보냈음을 알 수 있다.
  * 그럼으로 F+R의 장점은 노이즈가 적고 높은 전달성을 갖고 있다.
  * 모든 cpu코어로 부터 누수들을 관측할 수 있다.
  
# Meltdown

> 1 ; rcx = kernel address, rbx = probe array  
> 2 xor rax, rax  
> 3 retry:  
4 mov al, byte [rcx]  
5 shl rax, 0xc  
6 jz retry  
7 mov rbx, qword [rbx + rax]  

* 이번 장에서는 권한이 없는 user program에서 임의의 물리 메모리를 읽는 강력한 공격인 meltdown을 소개한다.
 1. 첫번째로 이 공격의 넓은 적용 가능성을 강조하기 위한 **Attack Setting**에 대해서 설명한다.
 2. 두번째로 어떻게 윈도우,리눅스,등의 개인 컴퓨터 그리고 안드로이드 모바일폰, 클라우드에서 meltdown이 mount될 수 있는지 말한다.
 3. 마지막으로 meltdown 구현이 kernel memory를 빠른 속도로 dump하는 것에 대해서 말한다.
* Attack setting
  * 개인컴퓨터와 가상 머신은 클라우드에 있다는 가정
  * 공격자는 임의의 권한이 없는 코드 실행을 갖고 있다는 가정(즉, 공격자는 일반 유저의 권한만큼 어떤 코드를 실행할 수 있다)
  * 공격자는 물리 메모리 접근을 하지 못한다는 가정
  * 시스템은 최신상태의 보안(ASLR,KASLR,SMAP..)의 상태
  * 버그가 없는 os이고, kernel주소를 접근하는 것에 취약점이 없는 상태이어야함
  * 공격자의 최종 목표는 유저의 데이터(password,private key)등을 추출하고자 함
  
## Attack Description
* 
