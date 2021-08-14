---
tags: python algorithm
toc: True
---

# map
* 여러 개의 데이터를 한 번에 다른 형태의 데이터로 변환하기 위해서 사용  

> map(변환 함수, 순환 가능한 데이터)   

* map이 반환하는 건 map class이다. 
> 변환 함수가 int로 설정이 되어있으면 받는 값에 따라서 type이 설정된다.
```python
    print(type(map(int,input().split()))) 
    n,m = map(int,input().split())
    print(type(n))  
    print(type(m))  
```
![image](https://user-images.githubusercontent.com/67637935/117431215-f3182100-af63-11eb-9282-bea1a570bb2c.png)   
  * cf) input() api의 default 반환형은 str이다.  

* map class를 list로 형 변환 시키면 list의 각각의 원소로 되어 저장된다. (tuple이면 tuple로 각 원소별 저장됨)
```python
print(list(map(int,input)))
```
![image](https://user-images.githubusercontent.com/67637935/117431807-9cf7ad80-af64-11eb-94ce-33f2a08365eb.png)

## Example 
* 1. When getting input data
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


# Sort
* sort는 매번 헷갈리는 것이 list나, dict자체적인 method를 사용할떄와 sorted라는 python 내장 method의 쓰임이 헷갈린다.

## sorted
    sorted(정렬할 데이터). 

    sorted(정렬할 데이터, reverse 파라미터). 

    sorted(정렬할 데이터, key 파라미터). 

    sorted(정렬할 데이터, key 파라미터, reverse 파라미터). 

    첫 번째 매개변수로 들어온 이터러블한 데이터를 새로운 정렬된 리스트로 만들어서 *반환해* 주는 함수입니다.  

## list.sort()
    리스트의 내부 요소를 정렬해주는 함수입니다.

    기본적으로는 오름차순으로 정렬이 됩니다.
    (작은것 이 앞으로 오고, 큰 값들이 뒤로 가는 정렬방식)

    sort 함수에서 주의해야할 것은 내부 요소의 데이터 타입이 같아야합니다.

    즉, 비교가 가능한 요소들만 있어야지 sort 함수가 작동할 수 있습니다.

    간단히 이야기해서 ["blockdmask", 1, 2, 3, 'a'] 이런 리스트는 내부에 문자열도 있고 정수도 있고 데이터 타입이 통일되지 않아서 비교가 불가능 하기 때문에 정렬이 불가능 한 것 입니다.
    
    비교 불가능 => 정렬 불가능


# List
* 다양한 method가 있지만 slice때의 범위 설정이 헷갈린다.

## list[n:m]
    1. 슬라이싱은 리스트 a의 a[N:M] 이라고 한다면 아래의 식을 만족합니다.  
    a[N] <= x < a[M]. 
    a[N] <= x <= a[M-1]. 
    N을 포함한 인덱스 부터 M을 포함하지 않는 인덱스 까지를 자르는 기능을 합니다.   
    2. 슬라이싱을 할때 맨 앞을 비워둔다면 a[:M] 이렇게 표현할 수 있으며 이는 아래의 식을 말합니다.  
    a[0] <= x < a[M]. 
    a[0] <= x <= a[M-1]\. 
    3. 뒤를 비워두는 a[N:] 이런 식이라면.  
    a[N] <= x < a[len(a)]. 
    a[N] <= x <= a[len(a) - 1].  
    len(a)는 리스트의 길이를 말합니다.  
    4. a[:] 이처럼 양쪽을 비워서 슬라이싱을 한다는 것은 리스트 전체를 복사하는것과 동일합니다.    
    a[0] <= x < a[len(a)]. 
    a[0] <= x <= a[len(a) - 1]. 


출처: https://blockdmask.tistory.com/425 [개발자 지망생]

# 양수 자릿수 확인:  len(str(number))
