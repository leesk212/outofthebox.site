---
tags: cloud docker
toc: True
---

> docker offical docker machine 접근
![스크린샷 2021-06-12 오후 12 00 46](https://user-images.githubusercontent.com/67637935/121763298-d29c4180-cb75-11eb-9ce0-bd956d26756f.png)
> private resistry에 push하기 위해 이름과 방법을 바꿔주었음
![스크린샷 2021-06-12 오후 12 03 41](https://user-images.githubusercontent.com/67637935/121763343-3b83b980-cb76-11eb-87c1-0d30e244537d.png)

## Dockerfile
Dockerfile은 Docker image생성을 위한 설정파일
Dockerfile에 설정된 내용으로 이미지를 생성합니다

### basit
```
FROM [올릴 image이름]
```
> docker build . -t <name:tag> 
### 1
```
FROM scratch
```
> docker build . -t <name:tag> 
> 빈 이미지 생성

### 2

```
FROM ubuntu
RUN apt -y update
RUN apt -y upgrade
```
> docker build . -t <name:tag> 

![스크린샷 2021-06-12 오후 12 47 37](https://user-images.githubusercontent.com/67637935/121764130-5f49fe00-cb7c-11eb-9dae-8f0470c56646.png)
자동으로 업데이트 업그레이드 까지 하는 것을 확인할 수 있음

### 3

```
FROM ubuntu
RUN apt -y update //shell code programing
RUN apt -y upgrade
CMD ["ls"]        
CMD ["ls", "-al"] //parameater를 따로 줘야해 
```
> docker run -it ls-ubuntu "data" 
를 통해서 명령어를 overwrite할 수 있음

### 4
```
FROM ubuntu
RUN ["apt","-y","update"]
RUN ["apt","-y","upgrade"]

ENDPOINT ["ls","-a"] <--append
CMD ["ls"] <----overwriting 개념
```

### 5: working directory 설정 & copy or 등등
WORKDIR /root
ADD FILE을 이미지에 추가합니다.
COPY 압축파일을 추가할 때 바로 추가하지 않고


```
RUN
ENTRYPOINT
CMD

FROM ubuntu
RUN ["ls" "-al"]
COPY hello.c /root [hello.c host에 있는 이미지를 생성할 때 컨테이너에게 가져다 주는 것]
RUN ["cp",..]

ENV --> interative 제거
EXPOSE 80 --> 포트 80를 열어 놓기
```

![스크린샷 2021-06-12 오후 3 12 59](https://user-images.githubusercontent.com/67637935/121767007-ad68fc80-cb90-11eb-84f7-efe915071887.png)

### 6. docker nginx dockerfile로 구현하기

![스크린샷 2021-06-12 오후 3 17 45](https://user-images.githubusercontent.com/67637935/121767120-59aae300-cb91-11eb-8673-566e4b25cbae.png)
> docker build -t my-ngnix .
> docker run -itd -p 9000:80 my-nginx 

### 7. docker pull ngnix
크기 비교
기본 허브에서 있는 이미지와 크기가 다름


### 8. 용량 줄여서 image만들기
용량은 base image에 따라 결정됨
redhat 계열: redhat, fedora, centos..


#### busy box
는 정말 필요한 것들만 입력 되기 때문에 1mb밖에 안함
#### alpine
##### alpine linux agnix설치
docerk run -itd --rm alpine (rm:종료시 제거)
busybox < alpine <ubuntu,redhat

