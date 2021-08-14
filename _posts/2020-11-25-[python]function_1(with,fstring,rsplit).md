---
tags: python
toc: True
---
# with

* with는 자체적으로 __enter()__과 __exit__()가 구현 되어 있음으로 자원을 획득하고 반납하였을 때 주로 사용한다.
* 자원은 한정 되어있기에, class와 같은 생성자 소멸자가 구현되어있는 것과 같이 편하게 라이프 사이클을 소멸시킬 수 있다.
```python
class Hello:
    def __enter__(self):
        # 사용할 자원을 가져오거나 만든다(핸들러 등)
        print('enter...')
        return self # 반환값이 있어야 VARIABLE를 블록내에서 사용할 수 있다
        
    def sayHello(self, name):
        # 자원을 사용한다. ex) 인사한다
        print('hello ' + name)

    def __exit__(self, exc_type, exc_val, exc_tb):
        # 마지막 처리를 한다(자원반납 등)
        print('exit...')
        
with Hello() as h:
    h.sayHello('obama')
    h.sayHello('trump')
```
```
[결과]
enter...
hello obama
hello trump
exit...
```
[with](https://m.blog.naver.com/PostView.nhn?blogId=wideeyed&logNo=221653260516&proxyReferer=https:%2F%2Fwww.google.com%2F)

# f string
* python 3.6이상부터 쉽게 string을 만들 수 있는 메서드이다.
```python
month = 1
while month <= 12:
    print(f'2020년 {month}월')
    month = month + 1
```
[f-string](https://blockdmask.tistory.com/429)

# rsplit
* split 하는 위치를 reversing하여 뒤에서 부터 reversing을 하는 구조이다.
```python
str1 = "//SF"
result = str1.split('/')
print(result)

# 실행 결과:
['', '', 'SF']
```
```python
str1 = "//SF"
result = str1.rsplit('/')
print(result)

# 실행 결과:
['', '', 'SF']
```
* rsplit을 해도 의도와는 다르게 나옴으로 이때는 파라미터 하나를 더 추가 시켜주면 된다.
```python
str1 = "//SF"
result = str1.rsplit('/', 1)
print(result)

# 실행 결과:
['/', 'SF']
```
* 이를 통해 의도대로 1번의 split만 이용되어서 분할을 진행할 수 있다.
[rsplit](https://lang-comp.tistory.com/5)
