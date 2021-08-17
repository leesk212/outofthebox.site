---
toc : True
tags : python 
---
# os
> import os

## 현재 디렉토리의 내부의 파일명들 추출
> os.listdir('.')
```python
file_list = os.listdir('./finalresult/')
```
## 경로 변경 
> os.chdir('현재 경로에서부터 가고싶은 경로')
```python
file_list = os.chdir('finalresult')
```


# file
## 파일 열기
> f = open(열고싶은파일,'r')
## 파일 읽기
### f.read()
* 전체 파일의 내용을 문자열로 돌려준다.
### f.realine()
* 전체 파일중 첫번쨰 줄만 읽어서 문자열로 
### f.readlines()
* 전체 파일을 \n(개행) 별로 나눠서 한줄을 하나의 list의 값으로 받기
