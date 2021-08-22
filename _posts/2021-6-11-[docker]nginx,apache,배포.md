---
tags: cloud docker
toc: True
---

# nginx docker run
> docker run -it -d -p 8080:80 nginx   
> 기존의 nginx 이미지를 다시 run 시키는데 이때 host의 8080과 docker의 ngnix서버의 80포트를 포트포워딩을 해주는 방식  
## nginx 서버에서 렌더링하는 페이지를 host machine과 연동시키는 과정
> (docker에서 nginx target server bash열기)  
> docker exec -it (nginx_docker_id) bash     
> (host에서 연동시킬 directory 생성)  
> docker run -d -p 8080:80 -v /Users/leekatme/Desktop/nginx_test/html:/usr/share/nginx/html ngnix  
![스크린샷 2021-06-12 오전 3 07 43](https://user-images.githubusercontent.com/67637935/121731018-5e3cb080-cb2b-11eb-8a7e-b362d23fccce.png)
![스크린샷 2021-06-12 오전 3 08 40](https://user-images.githubusercontent.com/67637935/121731133-7e6c6f80-cb2b-11eb-8bd0-c49beb5f56fa.png)
![스크린샷 2021-06-12 오전 3 09 20](https://user-images.githubusercontent.com/67637935/121731215-97752080-cb2b-11eb-9f8c-0e3122d660f3.png)
![스크린샷 2021-06-12 오전 3 09 28](https://user-images.githubusercontent.com/67637935/121731220-9ba13e00-cb2b-11eb-8438-ec35510294d2.png)

# apache run
--> apache
> hub 접근. 
> httpd run. 
> docker run -dlt -p 9000:80 --name (내가 원하는 이름) httpd
![스크린샷 2021-06-12 오전 3 15 09](https://user-images.githubusercontent.com/67637935/121731824-65b08980-cb2c-11eb-8f7a-382bef91f3c9.png)
> docker exec -it (docker_id) bash
## 컨테이너 이름 설정은 겹칠 수 없음
## 동일하게 mapping하는 과정 추가 포함
>![스크린샷 2021-06-12 오전 3 19 23](https://user-images.githubusercontent.com/67637935/121732262-fd15dc80-cb2c-11eb-8de0-653d6201a9d4.png)  
![스크린샷 2021-06-12 오전 3 19 40](https://user-images.githubusercontent.com/67637935/121732299-08690800-cb2d-11eb-807d-02a8cd85e34e.png)
* ip또는 port번호가 기억이 나지 않으면 docker inspect (docker-name 실행)

# netstat 명령어 확인
netstat -lntp 
> listening process들의 port 주소를 확인해서 사용하라!


> ![스크린샷 2021-06-12 오전 3 24 06](https://user-images.githubusercontent.com/67637935/121732817-a5c43c00-cb2d-11eb-9c9d-4cad0c6c9c6a.png)

![스크린샷 2021-06-12 오전 3 25 04](https://user-images.githubusercontent.com/67637935/121732929-cab8af00-cb2d-11eb-80c2-10d6b8ca8351.png)

![스크린샷 2021-06-12 오전 3 25 34](https://user-images.githubusercontent.com/67637935/121732977-db692500-cb2d-11eb-8980-956157a2d600.png)

# 마지막 스크린샷 고민
docker run -it[interative or terminal 붙인다--> 팔다리 붙이기]
           -v [volume mapping: 특정 폴더와 연동시킴] 
           -p [필요한 포트들]
           -d [background]
           "명령어"
           --rm [빠져나오면 도커의 container도 제거가됨]
![스크린샷 2021-06-12 오전 11 16 17](https://user-images.githubusercontent.com/67637935/121762233-9c5bc380-cb6f-11eb-82a4-e46ce0574ccb.png) ls
![스크린샷 2021-06-12 오전 11 16 58](https://user-images.githubusercontent.com/67637935/121762249-b4cbde00-cb6f-11eb-8ae1-c3a646598b4d.png) bash

docker run --rm it ubuntu

# docker 실행중인 ps 전체 지우기
![스크린샷 2021-06-12 오전 11 22 27](https://user-images.githubusercontent.com/67637935/121762380-78e54880-cb70-11eb-9583-911177918c56.png)
> docker rm `docker ps -a -q`

# image 다뤄보기
## 이미지 지우기
> docker rmi httpd
![스크린샷 2021-06-12 오전 11 24 22](https://user-images.githubusercontent.com/67637935/121762421-bf3aa780-cb70-11eb-8aa7-d25e2a583616.png)
## 이미지 생성 


## 이미지 저장
1. commit
-> net-tools를 설치한 image를 남겨두고 싶다.
> 이떄 쓰는 명령어가 commit
> ![스크린샷 2021-06-12 오전 11 28 19](https://user-images.githubusercontent.com/67637935/121762502-4ab43880-cb71-11eb-9d21-fa8aa35c0a90.png)
> ![스크린샷 2021-06-12 오전 11 28 41](https://user-images.githubusercontent.com/67637935/121762513-57d12780-cb71-11eb-8346-e48860e9c8e7.png)
docker images를 다음과 같이 Size로 생기게됨
  * commit의 최고의 장점: 하나의 image로 모든 부서에서 동일한 환경에서 사용할 수 있음 배포를 할 수 있음
2. save / load : commit후 Layer에서 쓴다. (layering을 갖고 있는다 아니다)
생성된 이미지를 local에 저장하고 싶다.
> docker save my_first_contatiner:1.0 -o ub.tar

생성된 이미지를 다시 container에 Load하고 싶다.
> docker load -i ub.tar

![스크린샷 2021-06-12 오전 11 37 33](https://user-images.githubusercontent.com/67637935/121762688-94e9e980-cb72-11eb-9de7-fb7504749a05.png)
3. export / import : 구조가 다름 다운로드해서 하는 것 
실행중인 docker ps에서 export 하고 싶음
![스크린샷 2021-06-12 오전 11 38 45](https://user-images.githubusercontent.com/67637935/121762739-cc589600-cb72-11eb-9bcd-d57add7204d5.png)

실행중인 docker ps로 import 하고 싶음
![스크린샷 2021-06-12 오전 11 40 13](https://user-images.githubusercontent.com/67637935/121762764-f447f980-cb72-11eb-90b5-3e365a751321.png)

docker images prune --> none이라고 되어있는 것을 제거함

![스크린샷 2021-06-12 오전 11 45 03](https://user-images.githubusercontent.com/67637935/121762871-a1227680-cb73-11eb-8ea9-8a4a7726f760.png)


### docker hub --> 다운 받았으면 다시 올릴 수도 있음
* docker push / pull로 hub에 저장하고 가져오고 사용
> * docker tag ub2:1.0 leesk212/ub2:1.0
> (이름을 바꿔주는 과정, 바꿔야 push가 돼) 
> docker push leesk212/ub2:1.0 [아이디/올릴 이미지:tag]
![스크린샷 2021-06-12 오전 11 51 20](https://user-images.githubusercontent.com/67637935/121763129-8270af80-cb74-11eb-9a19-57acddafdbf0.png)

* docker pull leesk212/ub2:1.0
> ![스크린샷 2021-06-12 오전 11 53 46](https://user-images.githubusercontent.com/67637935/121763167-d9768480-cb74-11eb-9fdb-b0887d1077ed.png)

### docker registry
![스크린샷 2021-06-12 오전 11 52 20](https://user-images.githubusercontent.com/67637935/121763150-a633f580-cb74-11eb-8a11-d0d1d0f42be1.png)
![스크린샷 2021-06-12 오전 11 51 45](https://user-images.githubusercontent.com/67637935/121763139-90becb80-cb74-11eb-8373-500bc9a860f4.png)

