---
tags: linux ssh open-ssh SEEDLAB
toc: True
---

# 만들고 싶은 환경
* 실습 수업을 진행하기위한 sandbox환경을 만드는 것을 목표로한다.
* 최소 20명~30명정도 되는 접속의 트래픽이 최대로 한번에 몰릴 가능성이 높다.
* 가장 편한 방법은 아무리 생각해도 docker나 AWS로 서버를 배포하는 것이 좋을 것이다.
* CSP에게 트래픽 처리와 이용자 접근에 대한 부담을 넘기는 방식이다.
* 하지만 제약사항이 각각 두가지가 있었다. 일단 Software Attack, H/W Attack을 진행하기 위해서는 Super user 권한을 줘야한다. 
* 그러게 되면 S/W 공격 같은 경우 이상이 없겠지만 , H/W 공격은 CPU Dependent하기에 CSP를 활용한 배포는 배제시켯다.

## 구축 활용
* 그래서 CPU가 공격가능한걸로 정하고, Sandbox환경이어야 한다.
* CPU가 공격 가능하고, CSP를 사용하지 못하기에 대용량(?) 트래픽도 처리가 가능해야한다.
* 그래서 연구실 Xeon (Window)에 Virtualbox를 활용해 Sandobx환경을 구축하였다.
  * host CPU: Xeon 
  * host OS: Window
  * VM vendor: virtualbox
  * VM OS: SEEDLAB(ubuntu)

## Topology
* 이미지로 구성한 topology는 다음과 같다.

![image](https://user-images.githubusercontent.com/67637935/148340744-86f893f7-4803-475d-8333-f3475c5d8749.png)

# 구현 방법
* VM을 virtualbox를 사용하기에 VMware와 다른 방법으로 nat설정을 진행해줘야 한다.
* 다행히 seedlab 자체적으로 open-ssh는 빌트가 되어있음으로, virtualbox <-> host(window)와의 연결이 우선이었다.
* 이 과정을 진행해주기 위해서는 현재 host pc의 공인ip를 알아야한다.

![image](https://user-images.githubusercontent.com/67637935/148342197-911e5948-fc89-4e13-9030-fe6f51adce0a.png)

* VB는 VB host-only nat가 따로 설치가 되는 것을 확인했다.
* 하지만 내가하고자 하는 것은 외부 PC로도 해당 VB 의 실험 환경에 접근해야하기에 IPv4 주소 두개 모두 SEEDLAB과의 포트포워딩을 진행한다.

## ( host <-> VM ) 포트포워딩

![image](https://user-images.githubusercontent.com/67637935/148342406-fb719260-6ff4-4065-84c9-7cdbe5069688.png)

* 설정 ->  네트워크 -> 고급

![image](https://user-images.githubusercontent.com/67637935/148342456-21ade68c-11ed-4d6a-8614-b8216777b0d8.png)

* 고급 -> 포트 포워딩

![image](https://user-images.githubusercontent.com/67637935/148342499-3af2bd05-ea72-4e13-841b-420128e12b69.png)

* 127.0.0.1은 localhost에 대한 test를 위해서 작성해주었다. 
* 해당 환경으로 포트포워딩 진행 후, 정상적으로 host에서 vm(seedlab)에 접속이 가능한지 확인한다.

![image](https://user-images.githubusercontent.com/67637935/148342784-8beb3a2d-2f2a-4520-aa92-bc82230e7c19.png)

* 세가지 환경 모두 정상적으로 seedlab의 shell에 접속이 가능했다.
* 포트포워딩의 개념은 "터널을 만든다"로 생각하면 개념잡기가 쉽다. 수많은 네트워크 중, 내가 원하는 pc와의 통신을 위해 터널을 뚫는다는 느낌!
* A와 B가 통신하기 위해서(터널을 뚫기 위해서)는 A에서도 구멍을 뚫어야하고(내부 포트 설정), B에서도 구멍을 뚫어야 한다(외부 포트 설정)

## (host <-> otherPC) 포트 포워딩
* 이건 굳이 설정하지 않겠다. 
* ISP의 공유기를 활용하여 포트포워딩을 진행해주면 된다.
* 이때 A와 B가 뚫려있는 터널을 C까지 뚤어야하니, B의 외부포트가 이제는 내부포트가 되고 이를 받기위한 외부포트를 뚤어주면 된다.

## 최종
VM <-> host <-> otherPC이 완성되었다.

# Consideration
* 고려대 VPN 설정이 단순히 IP와 Port 번호만 제출하는 것임으로, 다수의 사용자가 접속할 수 있도록 username은 다양하게 설정할 수 있을 것 같다.
* 다음 Overview_2 에서는 다음의 상태를 진행하고자한다. 
