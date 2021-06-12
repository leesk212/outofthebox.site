# kubernetes
* 관리하는 시스템이라고 적혀있습니다.
* cluster management system에 더 가깝다는 것을 알게된다.
* ochastraction tool이라고 많이 쓰임
* 선장을 나타내는 그리스말 
* 쿠버네티스를 설치 하는 것:
## docker가 등장하기전
* 서버의 상태를 관리하기 위하 노력 -> 도커-> 쿠버네티스
* 서버 설치, cpu--> 네트워크 연결 방화벽 설정--> 서버를 구매할 때부터 매우 좋았던 것을 씀
* Ruby on rails
![스크린샷 2021-06-12 오후 5 33 40](https://user-images.githubusercontent.com/67637935/121770458-54a35f00-cba4-11eb-8d9d-0c86c30f1d47.png)
* Ruby 1.8을 쓰고 있었는데 업데이트가 되면 모든 영향을 보여주는 상태가 된다..
* 상태 관리가 매우 어렵기에, 어떤것을 했는지 알 수가 없음 --> 믿을 수 없기에, 똑같은 서버를 만들 수 없기에 서버관리자가 GOD --> 확장이 어려움
* dev-ops가 이제 매우 불필요해짐
* 상태를 이용하는 코드를 서버를 갖고 선언을 한다.
* 설정파일을 갖고 있으면 동일한 상태로 그대로 이용할 수 있음
* 코드로 관리할 수 있기에 리뷰할 수 있게

PaaS --> source코드만 있으면 서버를 올릴 수 있게 된다.
Neltlify --> server내에서 의존적으로 실행되게 됌
배포에 대한 새로운 페러다임을 이해해야햄

![스크린샷 2021-06-12 오후 5 42 19](https://user-images.githubusercontent.com/67637935/121770656-8963e600-cba5-11eb-804c-43530a599686.png)

모든 서비스를 다 그냥 docker로 올려버린다.
중간에 conatiner가 죽었을 때 그대로 이용할 수 있도록 해준다.
docker-gen 단순한 기능만 제공했고 아제 이것들을 묶어서 한번에 구축할 수m 있도록 해주는 과정이 필요해졌어
그래서 쿠버네티스가 생기게돼

# 컨테이너 오케스트레이션: 여러 대의 서버와 여러 개의 서비스를 편리하게 관리해주는 작업
* 스케쥴링 --> 10개가 되었던 20개가 되었던 스스로 해줄 수 있게돼
* 쉽게 서비스에 접근할 수 있게 함
* kubernetes--> 표준이 되었다.
# Service Mesh
* OSS에서 proxy라는 기술로 관리할 수 있게된다. retry를 쉽게 할 수 있게한다.

## kubernetes playgorund
https://www.katacoda.com/courses/kubernetes/playground


## kubernetes architecture
1. WHat is Pod?
2. Master node --> Worker Node

![스크린샷 2021-06-12 오후 6 07 04](https://user-images.githubusercontent.com/67637935/121771214-ff1d8100-cba8-11eb-9b94-29dab82dc92e.png)

3. container가 배포될 물리 서버 또는 가상 머신 
4. pod 주머니 안에 주머니 안에 conatiner
5. 디플로이먼트
6. 컨테이너의 ocastration tool이다. --> 숫한 컨테이너를 돌릴 때, 그것을 관리해주기 위해서는 반복적인 서버가 필요하다.
7. 웹 어플리케이션을 개발한다고 하면, 판매자가 물품을 올리는 기능, 판매자 입장에서 보면 판매 입자읃ㄹ --> 등등의 기능들이 합쳐져있음
8. Pod1개당 단위에서 실행을 할 수 있게 해줌
9. Pod
10. 


## minikube install
> 설정 그대로 따라하면 돼 --> 가상화 환경 필요

minikube start , core를 더 늘려야할 경우도 있음
