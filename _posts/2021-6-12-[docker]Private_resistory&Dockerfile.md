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
