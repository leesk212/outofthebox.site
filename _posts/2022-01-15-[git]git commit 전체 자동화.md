---
tags: git etc
toc: True
---

# git add, commit -m 자동화
``` git add . ; git commit -m ${data} ; git push```

# git 게정입력, token 입력 자동화
ref: <https://kibua20.tistory.com/88>
## Case 1. SSH 연결로 Git clone 하는 경우 

1. SSH Key 인증서 생성후 GitHub에 등록 

2. git clone 명령어 사용 

    ``` $ git clone git@github.com-{your_id}:{your_id}/{repo_name}.git  ```

 

## Case 2. HTTP 연결로 Git clone하는 경우 

    ```$ git clone https://[ID]:[PASSWORD or Access Token]@github.com/[ID]/myrepo.git```
