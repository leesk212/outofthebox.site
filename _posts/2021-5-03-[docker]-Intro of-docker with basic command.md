---
tags: cloud docker
toc: True
---

# Intro of docker 
* 컨테이너를 사용해 어플리케이션을 신속하게 구축,테스트 및 배포할 수 있는 소프트 웨어 프로그램
## Keyword
### Process 격리
### DevOps: 개발과 운영의 합성어 
  * 운영하는 입장에서 신입 사원들에게 환경을 구축하고 배포하는 것도 코스트가 많이 듦
  * 또는 인수인계 해주는 과정에서도 다른 사람에게 하던 것을 유지관리 하도록 보내기 위한 과정이 쉽지 않음
  * Docker가 여기서 엄청한 장점 : 그냥 컨테이너로 만들어서 배포만 하면 돼 
  * 각각의 격리시켜서 실행시킬 수 있다. 
  * python,java 이런 버전들을 매번 달라지면 매우 어려움 but docker가 생기면서 그런 어려움들이 필요없음    
### client - server architecture
### 도커 호스트
![스크린샷 2021-05-03 오전 8 53 53](https://user-images.githubusercontent.com/67637935/116831900-18680080-abed-11eb-9b62-39929555732a.png)
* Cf) VMWare / VirtualBox :PC(host machine) <-> Guest 
matchine)
#### Docker hup: 레지스트리
* Docker Image들이 저장된 곳(Image에서부터 만들어지게 됌)
* Client가 Docker Resgistory에게 특정 명령어를 사용해서 그 이미지를 Dokcer host에서 만들어 컨테이너로 생성한다.
* 우분투랑 레딧이랑 화살표가 다른 것을 볼 수 있는데, 우분투 같은 경우는 자체 Docker daemon에 있는 값을 갖고와서 올린다. (cache 느낌)
* 한번 Resistory에서부터 온 이미지는 Host에 남아있게 되어서 사용자가 특정 명령어를 사용하지 않는 이상 Registry에서 지워질 필요가 없다.
* 그럼으로 clinet 요청의 flow를 생각해보면 e.g.,container 생성하라!! --> 이미지로 부터 생성됨으로 host에서 있는지 없는지 확인을 하고 없으면 resistory에서 부터 가져와서 이미지를 만든다.

#### 이미지(image)
* 기본적으로 docker 레지스트리 hup에 있거나, docker host에 있다.

### URL
* https://www.docker.com/
* https://hub.docker.com/ --> 검색해보면 이것이 레지스트리고 이것이 허브에 있다.
* https://docs.docker.com/ --> 설치

### Install
* sudo docker run hello-world
> this command *downloads* a test image and *runs* it in a container, it *prints* information message and exits!  
> MAC   
> ![스크린샷 2021-05-03 오전 9 43 53](https://user-images.githubusercontent.com/67637935/116833427-135a7f80-abf4-11eb-9bf2-8719efe0e068.png)
* image의 이름이 hello-world임
* 저장소로부터 hello-world라고 있는 것을 dokcer-host로 다운로드했음
* 처음에 unable오류가 났다면 docker-host에서 값이 없기 때문에 locall에서 못찾았다는 뜻이고 download( == pull)한다는 뜻이다.

* ![스크린샷 2021-05-03 오전 9 45 59](https://user-images.githubusercontent.com/67637935/116833494-603e5600-abf4-11eb-89cd-d4a442c1e342.png)
* 격리된 환경에서 레지스트리에 있는 이미지로 부터 새로 만들어지는 대상을 실행함.  
* ![스크린샷 2021-05-03 오전 9 47 43](https://user-images.githubusercontent.com/67637935/116833953-f5424e80-abf6-11eb-9fc5-90186c792cc8.png)
* pull / create / build for run 

### Post-installation
 * sudo 권한을 docker 명령어의 권한을 추가해주는 과정
 * 도커를 자동 시작해주는 service등록 과정
 
* ![스크린샷 2021-05-03 오전 10 00 42](https://user-images.githubusercontent.com/67637935/116833847-6d5c4480-abf6-11eb-9417-678e8bcc11e8.png)

## docker life cycle
![스크린샷 2021-05-03 오전 10 55 24](https://user-images.githubusercontent.com/67637935/116835597-26724d00-abfe-11eb-8899-31f72eba01f2.png)
* hello-world 이미지 같은 경우는 바로 죽는 명령어 이기 때문에 stop 명령어를 안써도 된다.


## 기본 명령어 사용
### docker --help
* ![스크린샷 2021-05-03 오전 10 07 30](https://user-images.githubusercontent.com/67637935/116834051-6255e400-abf7-11eb-9c1b-a839bb0b60bd.png)
* 명령어의 도움말: docker run --help
### docker run
* docker run <image>
* docker run [option] image [command] [arg..]
  * command는 image에 대한 명령어이다.
* 이 명령어는 사실 3가지의 명령어를 합친 것이다.
> docker run ==.       
> 
> docker pull hello-wold 
> 
> docker create hello-world 
> 
> docker start -a hello-world  
> 

### docker ps
* ![스크린샷 2021-05-03 오전 10 09 34](https://user-images.githubusercontent.com/67637935/116834143-ab0d9d00-abf7-11eb-9377-ab77e4fe370f.png)
* -a
 * 실행중인 컨테이너를 보여준다.
 * ![스크린샷 2021-05-03 오전 10 10 44](https://user-images.githubusercontent.com/67637935/116834171-d3959700-abf7-11eb-84a3-96f5c653a066.png)


### docker images
![스크린샷 2021-05-03 오전 10 12 15](https://user-images.githubusercontent.com/67637935/116834229-09d31680-abf8-11eb-8c1f-2445e2e9b87d.png)
![스크린샷 2021-05-03 오전 10 13 05](https://user-images.githubusercontent.com/67637935/116834251-28391200-abf8-11eb-9fc1-1cbdaf6f415c.png)

### docker pull <image>
* Pull an image or a repository from a registry
* ![스크린샷 2021-05-03 오전 10 14 02](https://user-images.githubusercontent.com/67637935/116834285-4a329480-abf8-11eb-9bdb-a2b3b59f5eb1.png)
* image 지우기 다시 pull하기
* ![스크린샷 2021-05-03 오전 10 41 20](https://user-images.githubusercontent.com/67637935/116835120-1ce7e580-abfc-11eb-8213-5efa72ad29c3.png)
* hash 값으로 부터 latest image id가 된 것이다.
* docker ps - a로 확인해도 없는 것이 정상

### docker create <image>
> local로 이미지 다운 받은 것을 container로 올리는 과정 
* docker images 로 pull 했던 docker images가 있는지 확인하기
* ![스크린샷 2021-05-03 오전 10 44 25](https://user-images.githubusercontent.com/67637935/116835225-8a941180-abfc-11eb-8485-e07d9934da8c.png)
* docker create hello-world
* ![스크린샷 2021-05-03 오전 10 45 10](https://user-images.githubusercontent.com/67637935/116835251-a3042c00-abfc-11eb-8155-6fdd56aa3b4d.png)

### docker start <container>
* docker ps -a 의 결과물로 나온 현재 올릴 수 있는 container의 아이디를 확인한 다음에 start뒤에 container의 아이디를 적어줘서 해당 이미지를 실행시킨다.
* ![스크린샷 2021-05-03 오전 10 47 35](https://user-images.githubusercontent.com/67637935/116835340-fb3b2e00-abfc-11eb-90e8-f634430a7fe5.png)
* Status가 Created 에서 ㄸExited가 된 것을 확인할 수 있다.
* 이 이유는 hello-world 이미지의 역할이 한번 뿌려지고 바로 끝나는 이미지이기 때문에, 보기 위해서는 docker attach를 사용해야하만 한다.
* docker start -a hello-world
* ![스크린샷 2021-05-03 오전 10 52 46](https://user-images.githubusercontent.com/67637935/116835493-b368d680-abfd-11eb-882a-7a0a1da5d567.png)

### docker attach <container>
* docker attach 실행시켜주면 되는데
* start를 할때 -a option을 주면 된다.


### docker rmi <image>!

* Remove image
* ![스크린샷 2021-05-03 오전 10 15 41](https://user-images.githubusercontent.com/67637935/116834330-84039b00-abf8-11eb-804a-b92cbb444e24.png)
* ERR) a2945ccde2a container가 이 이미지를 사용하고 있다. 그렇다면 containter를 삭제를 해줘야한다. ( ps -a 후 rm 으로 container 삭제 ) 
* ![스크린샷 2021-05-03 오전 10 39 21](https://user-images.githubusercontent.com/67637935/116835069-d2feff80-abfb-11eb-8b9a-a190267b0c6c.png)

### docker rm <id> 
![스크린샷 2021-05-03 오전 10 17 48](https://user-images.githubusercontent.com/67637935/116834403-d0e77180-abf8-11eb-8e73-424dec62b999.png)
![스크린샷 2021-05-03 오전 10 18 18](https://user-images.githubusercontent.com/67637935/116834412-e2307e00-abf8-11eb-9082-6f503314cdb4.png)

 * id 를 지울때 다 쓸 필요는 없고 구분되는 해쉬값까지만 적어주어도 지우는 것이 가능하다.
