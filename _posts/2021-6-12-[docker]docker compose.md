---
tags: cloud docker
toc: True
---
# docker compose란?
* compose: 구성하다 (make up)
* applications
* YAML "야믈"파일 XML -> JSON -> YAML --> key:value (pair)
  * docker-compose.yml
  ``` 
  version: "3.9"
  services:
    web:
      build:
      ports:
        - "5000:5000"
      volumes:
        - .:/code
        - logvolume01: /var/log
      links:
        - redis
     redis:
      image: redis
  volumes:
    logvolume01: {}
  ```
  * indentation 중요!!

Define and run multi-conatiner applications with Docker
*$ docker-compose up/down/ps/config*
$ docker 명령어 사용가능함
$ docker-compose up < 특정_container 이름>
$ docker-compose down

$ docker-compose up <---docker-compose.yml

## 작성했던 dockerfile 문법적 오류 확인: docker-compose config
![스크린샷 2021-06-12 오후 3 53 38](https://user-images.githubusercontent.com/67637935/121767923-5b2ada00-cb96-11eb-8f56-857d09dc322f.png)

이후 docker-compose ps로 실행되고 있는 docker-compsoe 확인하기
![스크린샷 2021-06-12 오후 3 56 58](https://user-images.githubusercontent.com/67637935/121768013-d1c7d780-cb96-11eb-8a23-e8bbd35941e2.png)

$ docker-compose down <-- 
![스크린샷 2021-06-12 오후 4 00 16](https://user-images.githubusercontent.com/67637935/121768114-4864d500-cb97-11eb-922f-28945bf327a6.png)

### wordpress + mysql --> 

$ mysql -u root -p

$ mysql > show databases

$ docker run --name some-mysql -it \
-e MYSQL_ROOT_PASSWORD=123 -d mysql bash

![스크린샷 2021-06-12 오후 4 53 40](https://user-images.githubusercontent.com/67637935/121769470-bd87d880-cb9e-11eb-96c4-fe9f0e0c0836.png)

두개의 container가 하나의 container처럼 사용이 가능해질 수 있음

db 연동 실습부분은 m1 chip에서 진행불가

# 연동 과정을 docker-compose.yaml파일에 넣어서 실행시켜보기
![스크린샷 2021-06-12 오후 5 02 16](https://user-images.githubusercontent.com/67637935/121769675-f1afc900-cb9f-11eb-90c3-cace647224a8.png)

두가지 service를 진행하는데, 이때 2가지 중에서 하나의 이름은 db1, 나머지의 이름은 web1이고 web1이 의존하는 db는 db1이다.
이때 m1chip에서는 호환성으로 실습이 불가능하니,
```platform: linux/x86_```
이 라인을 추가해준다.
![스크린샷 2021-06-12 오후 5 05 18](https://user-images.githubusercontent.com/67637935/121769754-5e2ac800-cba0-11eb-92a0-35d41d5aa653.png)
성공적인 상태 확인
web2도 성공적으로 다운로드가 된다.

![스크린샷 2021-06-12 오후 5 05 47](https://user-images.githubusercontent.com/67637935/121769769-6edb3e00-cba0-11eb-8066-b420368c59e4.png)

![스크린샷 2021-06-12 오후 5 06 38](https://user-images.githubusercontent.com/67637935/121769792-8d413980-cba0-11eb-804d-171079484783.png)
![스크린샷 2021-06-12 오후 5 08 51](https://user-images.githubusercontent.com/67637935/121769841-dd200080-cba0-11eb-9e15-c60b98608992.png)



![스크린샷 2021-06-12 오후 5 13 25](https://user-images.githubusercontent.com/67637935/121769968-80711580-cba1-11eb-9393-5c7a5ed1d9f4.png)
![스크린샷 2021-06-12 오후 5 14 10](https://user-images.githubusercontent.com/67637935/121769986-9b438a00-cba1-11eb-8447-5bbfcbbcdd7c.png)


