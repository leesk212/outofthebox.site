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
