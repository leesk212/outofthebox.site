---
tags: git
toc: True
---
# git branch

github 저장소의 branch를 통해 하나의 git 저장소에서 다양한 사람들의 작업을 분할해서 할 수 있다.


# git checkout __name__

만약 하나의 프로젝트에서 a,b,c,d,e가 각각 git의 브랜치를 달리해서 작업을 했다고 가정해보자

```
git brach -a
```
를 통해 전체적인 git의 branch종류들을 shell에서 확인할 수 있을 것이다.
a,b,c,d,e 5명 임으로 5명의 branch종류가 보일 것이다.

이때 a의 프로젝트의 내용을 내 파일에서 보고 싶다면
```
git merge origin/a 

```
를 통해 a의 내용을 나의 파일에 복사할 수 있다. 이때 충돌 되는 파일이 있다면 git에서 자동으로 충돌이 되었다고 알려줄 것이다.

다음은 내 branch가 아닌 다른 사람의 branch를 확인하는 것이다.

이때는 

```
git checkout a
```
를 통해 다른 사람의 branch로 이동할 수 있게 된다.
만약 이동이 제한된다면 다른 사람의 update된 파일을 다 다운받지 않거나 나의 local에서의 git 업데이트가 모두 되지 않았기 때문에, 
나의 local의 data들을 모두 넣기 위해서는 
```
git add .
git commit -m '__change mesg__`
```
를 통해 업데이틀 해주면 된다.

