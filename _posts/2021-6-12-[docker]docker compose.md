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
