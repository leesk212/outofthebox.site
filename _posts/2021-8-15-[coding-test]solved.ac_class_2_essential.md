---
toc : True
tags : coding-test python algorithm
---
```python
while True: 
  s = list(map(int,input().split(' ')))
  if sum(s)==0:
    break
  s = sorted(s,reverse=True)
  if s[0]**2 == s[1]**2 + s[2]**2:
    print('right')
  else:
    print('wrong')
```

# ACM 호텔
* link: https://www.acmicpc.net/submit/10250/31938169

* 30분 초과, ERROR 판단 부족
* 이유: 예외처리의 차이점 
 * 놓쳤던 부분이 무엇일까.. 음.. 
 * 나머지가 0일때를 새악을 안해주었다
 > 생각이 들었다면 각 층수에 맞는 호수라는 것을 파악할 수 있었을 것이다.
 * 조금 더 많은 테스트 케이스를 생각하면서 문제를 풀도록 하자


```python
#수정전
N = int(input())
for _ in range(N):
  H,W,N = map(int,input().split(' '))
  ho = N // H + 1
  cheng = N % H 
  if cheng == 0:
    cheng = 1
  print(ho,cheng)
  print(str(cheng*100+ho))
```

    2
    6 12 10
    2 4
    402
    30 50 72
    3 12
    1203



```python
#수정후
for _ in range(int(input())):
    H,W,N = map(int,input().split())
    cheng = N%H
    ho = N //H +1
    if cheng == 0:
        cheng = H
        ho = ho -1
    print(a*100+b)
```

* 최소한 조건보다 더 큰 min을 설정하자


```python
from itertools import permutations as p

case, answer = map(int,input().split(' '))
s = list(map(int,input().split(' ')))
s_p = p(s,3)
min=1000000
return_ = []
for each_permutation_result in s_p:
  if sum(each_permutation_result) <= answer:
    if abs(sum(each_permutation_result)-answer) < min:
      min = abs(sum(each_permutation_result)-answer)
      return_ = each_permutation_result

print(sum(return_))
```

    5 21
    5 6 7 8 9
    21


# python reverse 쓰는법!!!
> list(reversed(list__want))


```python
while True:
  s = list(input())
  if s[0] == '0':
    break
  len_s = len(s)
  if len_s % 2 == 00: # 짝수
    front = s[len_s//2:]
    back = list(reversed(s[:len_s//2]))
    if front == back:
      print('yes')
    else:
      print('no')
  else: #홀수 5//2 --> 2 [0,1] [3,4] 7 //2 3 [0,1,2] [4,5,6]
    if s[:len_s//2] == list(reversed(s[(len_s//2)+1:])):
      print('yes')
    else:
      print('no')

```

    1221
    yes
    12321
    yes
    0


# 이항계수란?
> 조합과 같다.

> return 을 할때 combination의 객체일 경우 list로 감싸줘야만 한다.


```python
from itertools import combinations as c
N,k = map(int,input().split())
s = []
for i in range(N):
  s.append(i)

s_c = list(c(s,k))
print(len(s_c))

```

    5 2
    10


# 체스판 다시 칠하기
* 구현은 모든 가능성을 생각하자 꼭..!


```python
N,M = map(int,input().split())
chess_pan = []
for _ in range(N):
  s = list(input())
  chess_pan.append(s)
can_mov_x = M-8
can_mov_y = N-8
ret = 1000000000
for y in range(can_mov_y+1):
  for x in range(can_mov_x+1):
    for for_answer in range(2):
      dif = 0
      if for_answer % 2 == 1:
        start_val = 'B'
      else:
        start_val = 'W'
      for yy in range(y,y+8):
        test_pan = chess_pan[yy][x:x+8]
        if start_val == 'W':
          if yy%2==0:
            answer = list('WBWBWBWB')
          else:
            answer = list('BWBWBWBW')
        else:
          if yy%2==0:
            answer = list('BWBWBWBW')
          else:
            answer = list('WBWBWBWB')
        for index in range(8):
          if test_pan[index] != answer[index]:
            dif = dif+1
        #print(test_pan,answer,dif)
      if dif < ret:
        ret = dif
print(ret)
```

    8 8
    BWBWBWBW
    WBWBWBWB
    BWBWBWBW
    WBWBWBWB
    BWBWBWBW
    WBWBWBWB
    BWBWBWBW
    WBWBWBWB
    0



```python
N = int(input())
answer = {}
for _ in range(N):
  s = input()
  if not s in answer:
    answer[s]=len(s)

print(answer)

answer_sorted = sorted(answer.items(),key=lambda k:k[1])

for each in answer_sorted:
  print(each)




```

    3
    ac
    sda
    c
    {'ac': 2, 'sda': 3, 'c': 1}
    ('c', 1)
    ('ac', 2)
    ('sda', 3)


# 단어정렬
* 생각해볼것:
  * ()--> python에서의 의미
  > tuple이라고 한다. 언제 쓰는 것이 적절하기에 list와 달리 묶을 수 있는 것일까..?

  * lambda는 정말 신비롭다.
  > 정렬의 우선순위를 줄 수 있다고 한다. 해당 튜플과 같이 진행하게 되면, 같은 첫번째 정렬의 위치가 되었을때 추가적으로 k[1]을 진행해 정렬을 진행한다.. 조금 더 연구해보고자 한다.


```python
N=int(input())
ans=[]

for idx in range(N):
  s = input()
  if not (len(s),s) in ans:
    ans.append((len(s),s))

ans = sorted(ans,key=lambda k: (k[0],k[1]))

for len, each in ans:
    print(each)
```

# math.gcd | math.lcm


```python
import math
a,b = map(int,input().split(' '))
print(math.gcd(a,b))
print(math.lcm(a,b))
```

# python 최적화된 수정렬
* 기존 sort는 O(nlog(n))이다.
  * python에서의 시간을 느리게 하는 요소는 입출력에 있다.
    * input --> sys.stdin.readline()
    * output--> sys.stdout.write()
    * 입출력을 sys로 바꾸면 시간이 반이상이 준다.
  * N의 범위차이를 확인하는 것이 point이다.
    * 100만까지기에 2^6약 이고, 
* merge sort는 O(N)이기에 sorted보다 더 빠르게 실행할 수 있다. --> 하지만 백준에서 이것도 pypy3로 제출해야함
* 백준기준, python3 보다 pypy3을 사용하면 시간내로 풀 수 있다.
  * 간단한 코드상에서는 Python3가 메모리, 속도 측에서 우세할 수 있는 것이고,
  * 복잡한 코드(반복)을 사용하는 경우에서는 PyPy3가 우세하기 때문에 (자주 쓰이는 코드를 캐싱하는 기능 )
* list.sort는 quick 정렬을 이용한다.
## ref
* merge sort 구현후 풀이: https://assaeunji.github.io/python/2020-05-06-bj2751/
* 3가지 sort에 대한 정리: https://velog.io/@uoayop/BOJ-2751-%EC%88%98-%EC%A0%95%EB%A0%AC%ED%95%98%EA%B8%B02Python


```python
N = int(input())
ans = []
for _ in range(N):
  each = int(input())
  ans.append(each)

ans.sort()

for each in ans:
  print(each)
```

    5
    5
    4
    3
    2
    1
    1
    2
    3
    4
    5



```python
import sys

ipt = sys.stdin.readline
arr = []

for i in range(int(ipt())):
    arr.append(int(ipt()))

for i in sorted(arr):
    print(i)
```


```python
import sys
a = int(sys.stdin.readline().strip())
```

# 나이순 정렬


```python
n = input()
ans = []
i = 0
for _ in range(int(n)):
    age, name = map(str,input().split(' '))
    ans.append((int(age),name,i))
    i=i+1

for each in sorted(ans,key=lambda k: (k[0],k[2])):
  print(str(each[0])+' '+(each[1]))
```

    5
    1



    ---------------------------------------------------------------------------

    ValueError                                Traceback (most recent call last)

    /var/folders/5h/1zddhv4j6f775fnql8mcznlr0000gp/T/ipykernel_7883/516653464.py in <module>
          3 i = 0
          4 for _ in range(int(n)):
    ----> 5     age, name = map(str,input().split(' '))
          6     ans.append((int(age),name,i))
          7     i=i+1


    ValueError: not enough values to unpack (expected 2, got 1)


# 좌표 정렬하기



```python
n = input()
ans = []
for _ in range(int(n)):
    x,y = map(str,input().split(' '))
    ans.append((int(x),int(y)))

for each in sorted(ans,key=lambda k:(k[0],k[1])):
  print(str(each[0])+' '+str(each[1]))
```

    5
    3 4
    1 1
    1 -1
    2 2
    3 3
    1 -1
    1 1
    2 2
    3 3
    3 4


# 수찾기

* 끝나고나서 확신이 들면 바꿔줘보기...!


```python
N = int(input())
A = list(map(int,input().split()))
M = int(input())
B = list(map(int,input().split()))

for each in B:
  if each in A:
    print('1')
  else:
    print('0')
```

    5
    4 1 5 2 3
    5
    1 37 9 5
    1
    0
    0
    1



```python
from sys import stdin, stdout
n = stdin.readline()
N = set(stdin.readline().split())
m = stdin.readline()
M = stdin.readline().split()

for l in M:
    stdout.write('1\n') if l in N else stdout.write('0\n')
```

# 소수판별 


```python
3%2
```




    1




```python
N = int(input())
s = sorted(list(map(int,input().split())))
ans=0
for each in s:
  if each!=1:
    for obj in range(2,1001):
      if obj <= each:
        if each == obj:
          ans=ans+1
        elif each % obj == 0:
          break
print(ans)
```

    5
    1 3 5 7 8
    3


# 카드 2
## deque 사용법


```python
from collections import deque
N = int(input())
a = deque()
for _ in range(N):
  a.append(_+1)
# 1 2 3 4 5 6
while True:
  if len(a)==1:
    print(a[0])
    break
  a.popleft()
  a.append(a.popleft())

```

    6
    4


# 괄호


```python

```

#숫자카드2


```python

```

# 스택

* deque와 list는 상호간의 형변환이 가능하다.
* rstript() method를 붙이면 stdin 사용이 가능하다 (거지같다)


```python
len(list(deque((1,2,3))))
```




    3




```python
from collections import deque 
from sys import stdin, stdout
stack = deque()
for _ in range(int(stdin.readline().rstrip())):
  cmd = stdin.readline().rstrip()
  if ' ' in cmd:
    cmd,val = cmd.split(' ')
    stack.appendleft(val)
  else:
    if cmd == 'top':
      if len(list(stack)) != 0:
        print(stack[0])
      else:
        print(-1)
    elif cmd == 'empty':
      if len(list(stack)) == 0:
        print(1)
      else:
        print(0)
    elif cmd =='size':
      print(len(list(stack)))
    elif cmd =='pop':
      if len(list(stack)) == 0:
        print(-1)
      else:
        print(stack.popleft())

```


```python
# 다른사람코드
import sys     
n= int(sys.stdin.readline().rstrip())
lst=[]
for i in range(n):
    command= sys.stdin.readline().rstrip().split()
    string= command[0]
    if string == 'push':
        lst.append(int(command[1]))
    elif string == 'top':
        print(lst[-1] if lst else -1)
    elif string=='pop':
        print(lst.pop() if lst else -1)       
    elif stirng=='size':
        print(len(lst)) 
    elif string=='empty':
        print(0 if lst else 1)
```

# 큐

> python if 한줄쓰기


```python
from collections import deque 
from sys import stdin, stdout
queue = deque()
for _ in range(int(stdin.readline().rstrip())):
  cmd = stdin.readline().rstrip()
  if ' ' in cmd:
    cmd,val = cmd.split(' ')
    queue.appendleft(val)
  else:
    if cmd == 'front':
      if len(list(queue)) != 0:
        print(queue[0])
      else:
        print(-1)
    elif cmd == 'empty':
      if len(list(queue)) == 0:
        print(1)
      else:
        print(0)
    elif cmd =='size':
      print(len(list(queue)))
    elif cmd =='pop':
      if len(list(queue)) == 0:
        print(-1)
      else:
        print(queue.pop())
    elif cmd =='back': 
      if len(list(queue)) != 0:
        print(queue[-1])
      else:
        print(-1)


```


```python
from collections import deque 
queue = deque()
for _ in range(int(input())):
  cmd = input()
  if ' ' in cmd:
    cmd,val = cmd.split(' ')
    queue.appendleft(val)
  else:
    if cmd == 'front':
      if len(list(queue)) != 0:
        print(queue[-1])
      else:
        print(-1)
    elif cmd == 'empty':
      if len(list(queue)) == 0:
        print(1)
      else:
        print(0)
    elif cmd =='size':
      print(len(list(queue)))
    elif cmd =='pop':
      if len(list(queue)) == 0:
        print(-1)
      else:
        print(queue.pop())
    elif cmd =='back': 
      if len(list(queue)) != 0:
        print(queue[0])
      else:
        print(-1)
```

# deque


```python
from collections import deque 
from sys import stdin, stdout
deq = deque()
for _ in range(int(input())):
  cmd = input()
  if ' ' in cmd:
    cmd,val = cmd.split(' ')
    if cmd == 'push_front':
      deq.appendleft(val)
    elif cmd == 'push_back':
      deq.append(val)
  else:
    if cmd == 'front':
      if len(list(deq)) != 0:
        print(deq[0])
      else:
        print(-1)
    elif cmd == 'empty':
      if len(list(deq)) == 0:
        print(1)
      else:
        print(0)
    elif cmd =='size':
      print(len(list(deq)))
    elif cmd =='pop_front':
      if len(list(deq)) == 0:
        print(-1)
      else:
        print(deq.popleft())
    elif cmd =='pop_back':
      if len(list(deq)) == 0:
        print(-1)
      else:
        print(deq.pop())
    elif cmd =='back': 
      if len(list(deq)) != 0:
        print(deq[-1])
      else:
        print(-1)

```

    15
    push_back 1
    push_front 2
    front
    2
    back
    1
    size
    2
    empty
    0
    pop_front
    2
    pop_back
    1
    pop_front
    -1
    size
    0
    empty
    1
    pop_back
    -1
    push_front 3
    empty
    0
    front
    3


# 숫자카드 2

> python dictionary의 사용법에 대한 고찰 진행하였다.

* list에서의 count method보다 훨씬 더 접근 성이 빠르다.
* dictionary의 in의 method는 key에대한 변환을 진행한다.
* dictionary 형태는 dic[key]=value 의 형태이다.
* key와 value에 대해서 둘다 접근하고자 한다면 dic.items()를 진행하면 가능하다.
  * e.g) for a,b in dic.items()


```python
from sys import stdin, stdout

N = int(stdin.readline().rstrip())
A = list(map(int,stdin.readline().rstrip().split()))
dic_for_A = {}
for each in A:
  if each in dic_for_A:
    dic_for_A[each]=dic_for_A[each]+1
  else: 
    dic_for_A[each]=1

M = int(stdin.readline().rstrip())
B = list(map(int,stdin.readline().rstrip().split()))

for each in B:
  if each in dic_for_A:
    print(dic_for_A[each],end='')
  else:
    print('0',end='')
  print(' ',end='')
```

    10
    6 3 2 10 10 -10 -10 7 3
    8
    10 9 -5 2 3 4 5 -10
    2 0 0 1 2 0 0 2 


```python
#dictionary 이용하기
from pprint import pprint

N = int(input())
A = list(map(int,input().split()))
dic_for_A = {}
for each in A:
  if each in dic_for_A:
    dic_for_A[each]=dic_for_A[each]+1
  else: 
    dic_for_A[each]=1

pprint(dic_for_A)

```

    10
    6 3 2 10 10 -10 -10 7 3
    {-10: 2, 2: 1, 3: 2, 6: 1, 7: 1, 10: 2}


# 괄호


```python
from collections import deque 
A = [1,2,3]
box = deque(A)
box2 = deque(A)
for each in box:
  print(each)
box.pop()
print(box2)
del box2[0]
print(box2)
```

    1
    2
    3
    deque([1, 2, 3])
    deque([2, 3])



```python
from collections import deque 

N = int(input())
A = list(input())

box = deque(A)
box_2 = deque(A)

idx = 0
while True:
  if idx==0 and cur_check==')':
    print('NO')
    break
  cur_check = box[idx]
  check_idx = 1
  checked_val = box[check_idx]
  while True:
    if check_idx%2==1 and checked_val == ')':
      box.popleft()
      del box[check_idx]
      break
    check_idx=check_idx+1
    checked_val = box[check_idx]

  idx=idx+1
  

```


```python
from collections import deque 

N = int(input())
for _ in range(N):
  string = list(input())
  tested = deque(string)
  idx = 0
  passed_cnt = 0
  while True:
    print(''.join(list(tested)),idx,passed_cnt)
    if tested[idx] == '(':
      passed_cnt=passed_cnt+1
    if tested[idx] == ')':
      if idx == 0:
        print('NO')
        break
      if tested[idx-1] =='(':
        print(tested)
        print(idx,idx-1)
        del tested[idx]
        print(tested)
        del tested[idx-1]
        idx = 0 
        passed_cnt = 0
        print(tested)
        continue
    idx=idx+1

```

    1
    ()()()()(()()())()
    ()()()()(()()())() 0 0
    ()()()()(()()())() 1 1
    deque(['(', ')', '(', ')', '(', ')', '(', ')', '(', '(', ')', '(', ')', '(', ')', ')', '(', ')'])
    1 0
    deque(['(', '(', ')', '(', ')', '(', ')', '(', '(', ')', '(', ')', '(', ')', ')', '(', ')'])
    deque(['(', ')', '(', ')', '(', ')', '(', '(', ')', '(', ')', '(', ')', ')', '(', ')'])
    ()()()(()()())() 0 0
    ()()()(()()())() 1 1
    deque(['(', ')', '(', ')', '(', ')', '(', '(', ')', '(', ')', '(', ')', ')', '(', ')'])
    1 0
    deque(['(', '(', ')', '(', ')', '(', '(', ')', '(', ')', '(', ')', ')', '(', ')'])
    deque(['(', ')', '(', ')', '(', '(', ')', '(', ')', '(', ')', ')', '(', ')'])
    ()()(()()())() 0 0
    ()()(()()())() 1 1
    deque(['(', ')', '(', ')', '(', '(', ')', '(', ')', '(', ')', ')', '(', ')'])
    1 0
    deque(['(', '(', ')', '(', '(', ')', '(', ')', '(', ')', ')', '(', ')'])
    deque(['(', ')', '(', '(', ')', '(', ')', '(', ')', ')', '(', ')'])
    ()(()()())() 0 0
    ()(()()())() 1 1
    deque(['(', ')', '(', '(', ')', '(', ')', '(', ')', ')', '(', ')'])
    1 0
    deque(['(', '(', '(', ')', '(', ')', '(', ')', ')', '(', ')'])
    deque(['(', '(', ')', '(', ')', '(', ')', ')', '(', ')'])
    (()()())() 0 0
    (()()())() 1 1
    (()()())() 2 2
    deque(['(', '(', ')', '(', ')', '(', ')', ')', '(', ')'])
    2 1
    deque(['(', '(', '(', ')', '(', ')', ')', '(', ')'])
    deque(['(', '(', ')', '(', ')', ')', '(', ')'])
    (()())() 0 0
    (()())() 1 1
    (()())() 2 2
    deque(['(', '(', ')', '(', ')', ')', '(', ')'])
    2 1
    deque(['(', '(', '(', ')', ')', '(', ')'])
    deque(['(', '(', ')', ')', '(', ')'])
    (())() 0 0
    (())() 1 1
    (())() 2 2
    deque(['(', '(', ')', ')', '(', ')'])
    2 1
    deque(['(', '(', ')', '(', ')'])
    deque(['(', ')', '(', ')'])
    ()() 0 0
    ()() 1 1
    deque(['(', ')', '(', ')'])
    1 0
    deque(['(', '(', ')'])
    deque(['(', ')'])
    () 0 0
    () 1 1
    deque(['(', ')'])
    1 0
    deque(['('])
    deque([])
     0 0



    ---------------------------------------------------------------------------

    IndexError                                Traceback (most recent call last)

    <ipython-input-27-34dc4d57f3a7> in <module>()
          9   while True:
         10     print(''.join(list(tested)),idx,passed_cnt)
    ---> 11     if tested[idx] == '(':
         12       passed_cnt=passed_cnt+1
         13     if tested[idx] == ')':


    IndexError: deque index out of range



```python
from collections import deque 

N = int(input())
for _ in range(N):
  string = list(input())
  tested = deque(string)
  idx = 0
  while True:
    if len(list(tested))==0:
      print('YES')
      break
    if len(list(tested))==1:
      print('NO')
      break
    try:
      if tested[idx] == ')':
        if idx == 0:
          print('NO')
          break
        if tested[idx-1] =='(':
          del tested[idx]
          del tested[idx-1]
          idx = 0 
          continue
    except:
      print('NO')
      break
    idx=idx+1

```

    1
    ((
    NO


# 요세푸스 문제 0 
* 이해가 잘 안돼..
> N = 7, K = 3 이라면 1번부터 7번까지의 사람들 중 1번을 시작으로 3번째 순서에 있는 사람이 제거가 된다.
즉 3번이 먼저 제거가 되는 것이다.
그 다음은 4부터 시작하여 다시 3번째 사람이 제거가 된다. 즉 6이 제거가 된다.
* index 시작이 달라지네!!(point) 이해완료

## List내에서 for문 선언하기 "List comprehension"
> a=[] 

> for x in ragne(5): a.append(x)

> --> a = [ x for x in range(5) ]


```python
from collections import deque

N,K = map(int,input().split())
circle = deque([x for x in range(1,N+1)])
ans = []
while True:
  #print(circle)
  if len(list(circle))==0: break
  try:
    ans.append(circle[K-1])
    del circle[K-1]
    for _ in range(K-1): circle.append(circle.popleft())
  except Exception as ex:
    ans = ans + list(circle)
    #index가 작아진것
    break

print('<',end='')
index = 0 
for each in ans:
  if index+1==len(ans):
    print(str(each)+'>',end='')
    break
  print(str(each)+', ',end='')
  index=index+1
```

    7 3
    <3, 6, 2, 7, 5, 1, 4>[3, 6, 2, 7, 5, 1, 4]



```python
from collections import deque

queue = deque()
answer = []

n, k = map(int, input().split())

for i in range(1, n+1):
    queue.append(i)

while queue:
    print(queue)
    for i in range(k-1):
        queue.append(queue.popleft())
    answer.append(queue.popleft())

print("<",end='')
for i in range(len(answer)-1):
    print("%d, "%answer[i], end='')
print(answer[-1], end='')
print(">")
```

    7 3
    deque([1, 2, 3, 4, 5, 6, 7])
    deque([4, 5, 6, 7, 1, 2])
    deque([7, 1, 2, 4, 5])
    deque([4, 5, 7, 1])
    deque([1, 4, 5])
    deque([1, 4])
    deque([4])
    <3, 6, 2, 7, 5, 1, 4>

