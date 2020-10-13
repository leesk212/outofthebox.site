# 1. 순정 Linux를 ssh에 올리기
```
$sudo apt-get install openssh-server
```

# 2. ssh server를 재시작하기
```
$sudo /etc/init.d/ssh restart
```

# 3. host.allow를 sshd: All로 수정해주기
```
$sudo gedit /etc/hosts.allow
```
* sshd: All

# 4. sshd_config 수정하기
```
$sudo vim /etc/ssh/sshd_config
```
* PermitRootLogin : root 사용자의 로그인 허용 여부. (yes, prohibit-password, forced-commands-only, no) 중에서 설정해야 합니다. 설정하지 않으면 prohibit-password 가 됩니다.
yes 로 설정하세요.  
* PasswordAuthentication : 비밀번호 로그인 허용 여부. (yes, no). 설정하지 않으면 yes 가 됩니다.
yes 로 설정하세요.
* ChallengeResponseAuthentication : ChallengeResponse 라는 특이한 인증 허용여부. (yes, no). 설정하지 않으면 yes 가 됩니다.
no 로 설정하세요.

# 5. Port 추가
```
#Port 22
```
* 되어 있는 부분을 주석 해제 시키고
* 추가적으로 현재 포트포워딩한 컴퓨터의 외부 포트값을 같이 적어준다.

## Reference
* [sshd_config](https://blog.lael.be/post/7678)  
* [Linux ssh에 올리기](http://blog.naver.com/PostView.nhn?blogId=ssamba&logNo=129099379)
