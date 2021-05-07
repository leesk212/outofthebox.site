---
toc : true
---

# map
> 여러 개의 데이터를 한 번에 다른 형태의 데이터로 변환하기 위해서 사용  
> map(변환 함수, 순환 가능한 데이터)   

## map이 반환하는 건 map class이다. 
> 변환 함수가 int로 설정이 되어있으면 받는 값에 따라서 type이 설정된다.
```python
    print(type(map(int,input().split()))) 
    n,m = map(int,input().split())
    print(type(n))  
    print(type(m))  
```
![image](https://user-images.githubusercontent.com/67637935/117431215-f3182100-af63-11eb-9282-bea1a570bb2c.png)   
  * cf) input() api의 default 반환형은 str이다.
> map class를 list로 형 변환 시키면 list의 각각의 원소로 되어 저장된다.
```
print(list(map(int,input)))
```
![image](https://user-images.githubusercontent.com/67637935/117431807-9cf7ad80-af64-11eb-94ce-33f2a08365eb.png)

## Example 
### 1. When getting input data
```python 
n,m = map(int, input().split())
```
* input() api로 입력 값을 받게 되면 str type으로 받아진다.
* 이것을 split() method를 사용하면 split안의 인자에 따라서 나눠지고 list로 반환이 된다. 
* 그 list로 반환된 값을 int로 mapping 시키는 과정
```python
2D_array = []
for i in range(n):
  2D_array.append(list(map(int, input()))
```
