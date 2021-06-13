![스크린샷 2021-06-13 오후 3 32 59](https://user-images.githubusercontent.com/67637935/121797657-a27b9e00-cc5c-11eb-9601-ecd03f0b6502.png)

* 기본 단위가 container가 아니라 pod임 ( 물론 container에 접근 할 수 있음)
* container < pod < rs < deployment

# 1 (지난시간)
> kubectl create deployment <dpl_name> --image=

> kubectl get po/rs/deploy

# 2 yaml file을 사용해서 구성한다.
> kubectl apply -f <name.yml>

* 기본적으로 key-value 파일이다. 

> kubectl get deploy --watch 켜놓고 보는 것이 편함
![스크린샷 2021-06-13 오후 3 46 17](https://user-images.githubusercontent.com/67637935/121797970-80831b00-cc5e-11eb-8b22-1b1d81b77c45.png)

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
* 





# 삭제 2가지 방법
## kuberctl delete deploy nginx-name
## 
