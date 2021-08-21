---
toc : True
tags : python coding-test
---
# 1차원 list 초기화 선언
```python
visited = [False] * 9 #[0,0,0,0,0,0,0,0,0]
```
# 2차원 list 빠른 행 선언
```python
graph = [[]*n for _ in range(n)]
graph[0].append(0)
graph[1].append(0)
graph[2].append(0)
```
# 2차원 list중 중복제거
```python
whitelists = list(set([tuple(set(whitelist)) for whitelist in whitelists]))
```
# 시간형태 비교
```python
all_indicies = sorted(all_indicies, key=lambda x: datetime.datetime.strptime(x, 'winlogbeat-%Y.%m.%d'))
```

