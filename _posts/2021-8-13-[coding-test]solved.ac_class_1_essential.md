---
toc : True
tags : coding-test algorithm python
---

```python
a,b = map(str,input().split(' '))
print(int(a)+int(b))
```

    3 4
    7



```python
a,b = map(str,input().split(' '))
print(int(a)-int(b))
```


```python
a,b = map(str,input().split(' '))
print(int(a)/int(b))
```

    1 3
    0.3333333333333333



```python
a = list(map(str, input().split(' ')))
cnt = 0 
for each in a:
  if len(each) == 0:
    cnt=cnt+1

print(str(len(a)-cnt))
```

     Mazatneunde Wae Teullyeoyo
    3



```python
a,b = map(str,input().split(' '))
a,b = int(a),int(b)
if a > b:
  print('>')
elif a < b:
  print('<')
elif a == b:
  print('==')


```

    2 3
    <



```python
a = int(input())
for row in range(a):
  for colum in range(row+1):
    print('*',end='')
  print('')
```

    5
    *
    **
    ***
    ****
    *****



```python
print('Hello World!')
```

    Hello World!



```python
a = []
for _ in range(9):
  each = int(input())
  a.append(each)
print(max(a))
print(str(int(a.index(max(a))+1)))
```

    3
    29
    38
    12
    57
    74
    40
    85
    61
    85
    7



```python
test_cnt = int(input())
for _ in range(test_cnt):
  R,S = map(str,input().split(' '))
  R = int(R)
  S = list(S)
  for each_s in S:
    for iterate_cnt in range(R):
      print(each_s,end='')
  print('')
```

    2
    3 ABC
    AAABBBCCC
    5 /HTP
    /////HHHHHTTTTTPPPPP



```python
dan = int(input())
for _ in range(9):
  print(str(dan)+' * '+str(_+1)+' = '+str(dan*(_+1)))
```

    2
    2 * 0 = 0
    2 * 1 = 2
    2 * 2 = 4
    2 * 3 = 6
    2 * 4 = 8
    2 * 5 = 10
    2 * 6 = 12
    2 * 7 = 14
    2 * 8 = 16



```python
a,b = map(str,input().split(' '))
a,b = int(a),int(b)
print(a+b)
print(a-b)
print(a*b)
print(a//b)
print(a%b)
```

    7 3
    10
    4
    21
    2
    1



```python
print(ord(str(input())))
```

    A
    65


* ord: python의 ASCII로 출력해주는 api


```python
N = int(input())
S = list(map(int,input().split(' ')))

print(min(S),max(S))
```

    5
    20 10 35 30 7
    [20, 10, 35, 30, 7]
    7 35


* maping에 int로 맵핑하면 나눴던 각각의 값들이 int로 변경된다.


```python
N = int(input())
for _ in range(N):
  a,b = map(int,input().split(' '))
  print(a+b)
```


```python
N = 1000
for _ in range(N):
  try:
    a,b = map(int,input().split(' '))
    print(a+b)
  except: 
    break

```

    4



```python
N = 1000
for _ in range(N):
  try:
    a,b = map(int,input().split(' '))
    if a+b == 0:
      break
    else:
      print(a+b)
  except: 
    break

```


```python
S = list(map(int,input().split(' ')))
if S == [1,2,3,4,5,6,7,8]:
  print('ascending')
elif S == [8,7,6,5,4,3,2,1]:
  print('descending')
else:
  print('mixed')
```

    1 2 3 4 5 6 7 8
    [1, 2, 3, 4, 5, 6, 7, 8]
    ascending



```python
def make_(value):
  return_ = 0 
  for i in range(value):
    return_ = return_ + (i+1)
  return return_

N = int(input())
return_list = []
for _ in range(N):
  string = list(map(str,input().split('X')))
  answer = 0 
  for each in string:
    if not len(each) == 0:
      answer = answer + make_(len(each))
  print(answer)
```

    5
    OOXXOXXOOO
    10
    
    OOXXOOXXOO
    9
    
    OXOXOXOXOXOX
    6
    
    OOOOOOOOOO
    55
    
    OOOOXOOOOXOOOOX
    30
    



```python
N = int(input())
s = str(input())
answer_ = []
for each in s:
  answer_.append(int(each))
print(sum(answer_))
```

    11
    10987654321
    [1, 0, 9, 8, 7, 6, 5, 4, 3, 2, 1]
    46



```python

```
