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
마지막 스크린샷 고민
