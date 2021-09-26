---
toc: True
tags: git
---

# git branch 만들기
> git branch seokmin

# git branch 접근하기(전환하기)
> git checkout seokmin

# git add & commit으로 branch를 stream에 인식시켜주기 

# remote branch를 local branch로 따주기 
> (default는 master stream이기에 특정 branch를 복제하여  branch로 사용하고 싶을때)

> git checkout -b minseo origin/seokmin

> git add & commit 진행!

* 어느 stream을 헤더로 진행해줄지를 정해줘야함
> git push origin HEAD

> 이를 통해 seokmin branch가 아닌 main stream의 복제된 위치임을 확인가능
