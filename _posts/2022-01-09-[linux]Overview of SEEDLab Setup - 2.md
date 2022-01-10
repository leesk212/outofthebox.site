---
tags: seedlab
toc: True
---

# 관제란?
관제 대상기관의 IT 자원을 사이버공격으로부터 보호하기 위하여 보안 이벤트 및 로그 등을 실시간으로 감시 및 분석 하는 것을 뜻한다.

# 실습 Server 관제 필요한 것
1. admin, seed 계정에 대한 비정상적인 접근
2. 현재 서버에 접속한 사람 수
3. 현재 서버에 접속한 계정이름
4. 전체적인 트래픽
5. 과제하는 것을 log 파일로 드롭시킬 수 있는 기능

# 리눅스에서는 관제를 위한 "watch" 명령어가 존재한다.
* 필요한 기능을 하는 명령어들을 내가 원하는 초마다 반복해서 (Refresh하여)실행시켜주는 명령어이다. 
* ref: <https://hyunchang88.tistory.com/245>

![image](https://user-images.githubusercontent.com/67637935/148711976-88ccf03c-be8f-4b5c-99ab-06bcba21994b.png)

# 그럼 어떤 명렁어를 사용하면 구성에 필요한 것들을 추적할 수 있을까?
* last -d : 최근에 해당 서버에 접속하려는 계정을 보여준다. 
* last -f /var/log/btmp: 로그인 실패 기록을 보여준다.
* netstat -nap | grep :22 | grep ESTABLISH : 접속한 계정 중 22번 포트를 사용해서 들어온 pts를 출력한다.

![image](https://user-images.githubusercontent.com/67637935/148712722-c27d9edb-cded-46ee-86e0-bf8eb2e42217.png)

* 트래픽 확인
두가지 어플리케이션을 사용하고 있지만, 뭔가 마음에 들지 않는다.

# log로 drop을 어떻게 진행시킬까?
* ref: <https://unix.stackexchange.com/questions/56093/output-of-watch-command-as-a-list>

![image](https://user-images.githubusercontent.com/67637935/148712220-09a3262e-b0f9-42e0-b592-af8483b7fa52.png)

```watch -n1 'wc -l my.log | tee -a statistics.log'``` 명령어를 통해서 화면의 출력과 log파일을 drop 시켜주는 과정을 동시에 진행할 수 있다.
* 반복시키고 있는 명령어에 __파이프라인 + tee -a__ 명령어를 사용하면 해당 쉘이 실행시키고 있는 프로그램을 로그파일로 드롭 시킬 수 있다. 
