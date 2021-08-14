---
tags: cloud kubernetes 
toc: True
---
![스크린샷 2021-06-13 오후 3 32 59](https://user-images.githubusercontent.com/67637935/121797657-a27b9e00-cc5c-11eb-9601-ecd03f0b6502.png)

* 기본 단위가 container가 아니라 pod임 ( 물론 container에 접근 할 수 있음)
* container < pod < rs < deployment

# 1 
> kubectl create deployment <dpl_name> --image=

> kubectl get po/rs/deploy

# 2 yaml file을 사용해서 구성한다.
> kubectl apply -f <name.yml>

* 기본적으로 key-value 파일이다. 

> kubectl get deploy --watch 켜놓고 보는 것이 편함
![스크린샷 2021-06-13 오후 3 46 17](https://user-images.githubusercontent.com/67637935/121797970-80831b00-cc5e-11eb-8b22-1b1d81b77c45.png)
![스크린샷 2021-06-13 오후 5 01 20](https://user-images.githubusercontent.com/67637935/121799786-fd1af700-cc68-11eb-94a2-d4a212e7c787.png)

내가 생성하고 싶은 rs의 갯수들을 편하게 설정할 수 있음

## yaml파일 세부 분석
```
apiVersion: apps/v1
kind: Deployment //deplyment를 위한 yaml파일이다.
metadata:        //deployment의 세부정보
  name: nginx-deployment //deployment의 이름
  labels:
    app: nginx  //세부적으로 할당 될 nginx이름
spec:
  replicas: 3 //replicas의 갯수
  selector:
    matchLabels: //해당되는 pod의 이름은!?
      app: nginx //nginx
  template:
    metadata:
      labels:
        app: nginx //pod
    spec:
      containers:
      - name: nginx //세부적인 container는 다음과 같다.
        image: nginx:1.14.2
        ports:
        - containerPort: 80 
```


## expose를 yaml파일로 만들어보기!
```
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  tyep: LoadBalancer
  selector:
    app: nginx
  ports:
  - protocal: TCP
    port: 8040
    targetPort: 80
```
* 이서비스가 deployment의 spec위의 nginx의 label의 값과 selector의 app의 nginx의 값에 대해서 동일하게 적어줘야함
> kubectl get all (로 전체 image 확인)
![스크린샷 2021-06-13 오후 4 11 12](https://user-images.githubusercontent.com/67637935/121798543-fb016a00-cc61-11eb-9bc5-adc8ccefb7a6.png)



> minicube service list (로 ip 확인가능)
![스크린샷 2021-06-13 오후 4 11 43](https://user-images.githubusercontent.com/67637935/121798559-0d7ba380-cc62-11eb-80ca-f8d080fd0162.png)

* 8040 port로 안나오는 이유는 자체적으로 32340로 pending 되었기 떄문이다.
![스크린샷 2021-06-13 오후 4 12 39](https://user-images.githubusercontent.com/67637935/121798592-2f752600-cc62-11eb-8c4c-0102889d8169.png)

# 이제 이 index를 바꿔보는 실습하기
1. kubectl get po로 해당 pod 찾기 (난왜 3개지..?) --> nginx.yaml에서 3개로 설정했기에! 1개로 바꿔서 시간단축하기!
![스크린샷 2021-06-13 오후 4 16 01](https://user-images.githubusercontent.com/67637935/121798677-a7435080-cc62-11eb-871f-71879fe997ff.png)

2. 해당 pod를 bash로 열기
![스크린샷 2021-06-13 오후 4 17 14](https://user-images.githubusercontent.com/67637935/121798716-d35ed180-cc62-11eb-8cb2-a712b5e5b768.png)

3. cd /usr/share/nginx/html/index.html 으로 이동해주고 수정하기
![스크린샷 2021-06-13 오후 4 18 58](https://user-images.githubusercontent.com/67637935/121798757-10c35f00-cc63-11eb-88cb-a1c38175088a.png)

4. cat 또는 echo로 수정해주기

![스크린샷 2021-06-13 오후 4 23 16](https://user-images.githubusercontent.com/67637935/121798861-aa8b0c00-cc63-11eb-8e13-c1647f8d0b2d.png)

5. 3개의 pod가 생성이 되었다면 가장 첫번째 파드가 외부와 연결되어 있는 pod이다.

# 삭제 
## yaml파일 자체를 지워버림
> kuberctl delete -f ___.yaml  

![스크린샷 2021-06-13 오후 5 02 59](https://user-images.githubusercontent.com/67637935/121799827-394e5780-cc69-11eb-9897-74efe05312e5.png)

## kuberctl delete deploy nginx-name
## kuberctl delete src service-name
![스크린샷 2021-06-13 오후 4 48 56](https://user-images.githubusercontent.com/67637935/121799465-41a59300-cc67-11eb-9f79-6eb19e85d250.png)


# 최종 복습 yaml 실습
## 1. nginx label 변경
![스크린샷 2021-06-13 오후 4 51 35](https://user-images.githubusercontent.com/67637935/121799525-9f39df80-cc67-11eb-9648-3c21d274f072.png)

## 2. nginx service.yaml 변경
![스크린샷 2021-06-13 오후 4 50 46](https://user-images.githubusercontent.com/67637935/121799516-93e6b400-cc67-11eb-92ce-32e754b06392.png)

## 3. mimikube service list로 확인 및 url 접근 
![스크린샷 2021-06-13 오후 4 52 44](https://user-images.githubusercontent.com/67637935/121799556-c8f30680-cc67-11eb-9fb8-38bc5193a73d.png)
![스크린샷 2021-06-13 오후 4 53 17](https://user-images.githubusercontent.com/67637935/121799576-dc05d680-cc67-11eb-9a4b-df58aebe17f6.png)

## 4. pod확인 및 bash로 들어가기
![스크린샷 2021-06-13 오후 4 54 30](https://user-images.githubusercontent.com/67637935/121799605-0788c100-cc68-11eb-98bb-9a71a5560107.png)

## cd /usr/share/nginx/html/index.html 수정
![스크린샷 2021-06-13 오후 4 57 01](https://user-images.githubusercontent.com/67637935/121799675-61898680-cc68-11eb-8c4c-3cedb971209c.png)

## 안되면 많은 pod들 중에 하나 선택해서 그냥 해보면 됨, 그냥 순차적으로 하는 것을 추천

# deploy & service 파일을 하나로 합칠 수 있음
> --- 쓰고 사용
![스크린샷 2021-06-13 오후 5 07 40](https://user-images.githubusercontent.com/67637935/121799905-df01c680-cc69-11eb-808c-9bad69d5b1d7.png)
# 성공적
![스크린샷 2021-06-13 오후 5 09 20](https://user-images.githubusercontent.com/67637935/121799949-18d2cd00-cc6a-11eb-905a-6b4a1f6dfbfe.png)

