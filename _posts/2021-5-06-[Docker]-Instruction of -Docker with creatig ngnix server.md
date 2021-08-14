---
tags: cloud docker
toc: True
---

# Docker Practice -2 
## Docker run
### Command
#### $docker run -it ubuntu bash 
* docker run의 뜻은 새로운 컨테이너를 실행시키는 명령어이다.
* 이때 사용되는 것이 -i와 -t이고 i는 interactive으로써 키보드의 입력(Standard Input output)을 attatch 시켜라라는 뜻이다. t는 tty를 붙여라라는 뜻이다.
* 즉 -it를 사용함으로써 standard input output을 붙이라는 뜻이다.
* ubuntu는 어떤 image를 받을지 정한 명령어이다.
* bash는 ubuntu에서 실행할 명령어이다.
#### exit
* 컨테이너 완전히 종료
#### docker start -a _contatiner id_
* 방금 멈춘 docker를 다시 실행시키는 것
#### Ctrl+P or Ctrl+Q
* 컨테이너가 실행되된 쉘만 종료되고 docker container는 아직 살아있음 (ps로 확인) (잠시 빠져나오는 느낌) 
#### docker attach _conatiner id_
* 잠시 멈췄던 conatiner를 다시 쉘을 킨다.
#### docker rm _container id_
* 삭제에는 몇가지 방법이 있음
* $ docker rm < _container id_/Name > <---하나하나씩 삭제
* $ docker rm '$docker ps -a -q' <---전체 docker container 삭제
  * $ docker rm <id>, <id>, ... 
  * docker ps -a -q를 하면 container의 id를 argument로 다 받을 수 있기에 모든 image에 올라와있는 모든 docker들을 삭제할 수 있다.
  * 실행중인 conatiner들은 삭제가 안될 수도 있음 강제로 stop하게 하고 싶으면 -f 옵션을 주면 돼
#### docker conatiner prune
* stop된 container들만 제거한다.
### Practice
* ![스크린샷 2021-05-06 오전 6 01 16](https://user-images.githubusercontent.com/67637935/117208784-7b95a500-ae30-11eb-8170-c9315174c5e7.png)
  * 초기 임으로 local에 이미지가 없음으로 pull하였던 과정임을 확인할 수 있다.
* ![스크린샷 2021-05-06 오전 6 02 03](https://user-images.githubusercontent.com/67637935/117208858-9536ec80-ae30-11eb-8f72-49013c080a18.png)
  * 두번째 부터는 pull과정이 없다. 
  * ![스크린샷 2021-05-06 오전 6 02 33](https://user-images.githubusercontent.com/67637935/117208927-a7b12600-ae30-11eb-97a7-213739cf1507.png)
* hostname이 image의 container name이다.
* exit->start 와 Ctrl + P , Q -> attach ps 비교
  * ![스크린샷 2021-05-06 오후 1 12 44](https://user-images.githubusercontent.com/67637935/117240873-c0d7c800-ae6c-11eb-9cd6-1eb27beae3f4.png)
* docker container prune
  * ![스크린샷 2021-05-06 오후 1 36 31](https://user-images.githubusercontent.com/67637935/117242388-1c578500-ae70-11eb-88f1-996ff1711a27.png)

#### Review
* ![image](https://user-images.githubusercontent.com/67637935/117242470-4c068d00-ae70-11eb-8236-598c67de8f8e.png)  
* host name이 뒤가 container의 id이다.
* ![스크린샷 2021-05-06 오후 1 39 58](https://user-images.githubusercontent.com/67637935/117242608-8ec86500-ae70-11eb-8927-ece32d5621f4.png)  
* attach는 실행중인 conatiner에 input output을 붙이는 명령어이다.  
* attach를 붙여 버리면 현재 있는 실행중인 것 과 INPUT OUTPUT ERR를 붙이는 것이기 때문에 같이 명령어가 써지는 것을 확인할 수 있다.  
* ![스크린샷 2021-05-06 오후 1 42 36](https://user-images.githubusercontent.com/67637935/117242796-ec5cb180-ae70-11eb-866c-76b519b56b40.png)  
* exit로 꺼진 conatiner는 start 명령어를 통해 다시 컨테이너를 실행시킬 수 있다.

#### Advantage
* 이미지를 매번 어렵게 구을 필요 없이 단순히 올리는 것만으로 새로운 ubuntu를 계속 생성할 수 있다.

# Docker Web Server 
## Default:  Two new ubunbtu image
> $ docker run -it ubuntu bash.   
> $ apt -y upgrade && update.  
> $ apt -y nginx. 
> $ apt -y install netools. 
> $ apt -y install crul
> $ ifconfig  
> ![스크린샷 2021-05-06 오후 1 49 47](https://user-images.githubusercontent.com/67637935/117243296-ed421300-ae71-11eb-9513-2a03429c79d7.png)  
* 두개의 다른 ubuntu에서 각각의 nginx로 접근할 수 있도록 실행해줌  
### MAC --> fail  --> Success. 
> ![스크린샷 2021-05-06 오후 1 55 38](https://user-images.githubusercontent.com/67637935/117243736-be786c80-ae72-11eb-8d50-42160a15d00f.png).  

> $ service nginx satrt. 

> $ service --status-all. 

> ![스크린샷 2021-05-06 오후 1 58 00](https://user-images.githubusercontent.com/67637935/117243928-13b47e00-ae73-11eb-92fd-34a8d45d9375.png). 

## image create nginx server
* https://hub.docker.com/_/nginx 접속
* nginx검색 --> nginix라는 컨테이너가 있음
* Exposing external port부분에서 명령어 긁기
#### docker run -it -d -p 80:80 nginx
* -d : detach 모드임 : daemon으로 실행을 하라!! 
* -p : port mapping인데 지금 docker host 80 <---> conatiner 80과 연결을 하라!! [host:conatiner] 
* ![스크린샷 2021-05-06 오후 2 06 50](https://user-images.githubusercontent.com/67637935/117244606-4e6ae600-ae74-11eb-8539-80586afd20a0.png)

### 그럼 host를 8080으로 nginx를 만들게 된다면!?
* ![스크린샷 2021-05-06 오후 2 10 39](https://user-images.githubusercontent.com/67637935/117244901-d7821d00-ae74-11eb-8e85-4ae58c7d05b3.png)
* 8080의 ip 주소를 알아야 하기에
#### $ docker exec -it _8080-host-nginx-conatiner-id_ bash
* ![스크린샷 2021-05-06 오후 2 13 07](https://user-images.githubusercontent.com/67637935/117245091-2fb91f00-ae75-11eb-8ec9-7a6a9c9d5be1.png)
* ifconfig를 위해서 net-tools설치하기 (update upgrade)
* ![스크린샷 2021-05-06 오후 2 15 17](https://user-images.githubusercontent.com/67637935/117245248-7d358c00-ae75-11eb-8b4c-a13c0c3d50dd.png)
* Host 127.0.0.1:8080으로도 접근이 가능해
* ![스크린샷 2021-05-06 오후 2 15 58](https://user-images.githubusercontent.com/67637935/117245296-963e3d00-ae75-11eb-8f2c-9c6965bd73d2.png)
#### $ docker inspect _container name_
* ![스크린샷 2021-05-06 오후 2 29 58](https://user-images.githubusercontent.com/67637935/117246402-89bae400-ae77-11eb-81d8-ed3d2ba6b512.png)
* ip address까지 모두 잘 나옴 
* ![스크린샷 2021-05-06 오후 2 31 02](https://user-images.githubusercontent.com/67637935/117246479-afe08400-ae77-11eb-99ab-3ca21860f04b.png)

