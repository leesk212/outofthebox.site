---
tags: security-attack system-security ftz
toc: True
---

# 메모리 레이아웃 기초

버퍼 오버플로우 공격과 리버싱 같은 시스템 해킹기법을 위해서는 메모리 레이아웃을 반드시 이해해야 한다. 

만약 소스코드가 다음과 같이 있다면 메모리에 배치는 다음과 같이  **Heap**과 **Stack**에 저장이 진행된다.

```
//Filename:structure.c

#include <stdio.h>

int retVal = 0;
int outVal;

void function(int a,int b,int c){
  char buffer1[5];
  char buffer2[10];
}

int main()
{
  char string[] = "hello";
  char *ptr;
  stastic int output = 1;
  ptr = (char*) malloc(sizeof(string));
  
  function(1,2,3);
  printf("%s\n",string);
  return retVal;
}
```

|물리주소|메모리 세그먼트|저장 데이터|실제 데이터|
|------|---|---|-----|
|0x000000|Test(=Code)|실행명령|#include<stdio.h> \n int main(){ ... }|
|.|Data|전역,const,static 변수,초기화된 변수|int retVal = 0,static int output = 1;|
|.|BSS|초기화되지 않는 변수|int outval|
|.|Heap|동적 메모리|ptr = (char*)malloc(sizeof(string))|
|.|Heap과 Stack사이|힙과 스택의 여분공간|변수가 늘어날 경우 힙과 스택의 시작점 사이에 있는 이 공간에 할당|
|oxffffff|Stack|지역변수|char string[] = "hello";, char *ptr|


이때 gcc -o structure structure.c 를 통해 run 파일 ,structure을 생성하고  
gdb로 진입 후   
gdb structure을 진행하게 되면  
gdb내부에서 어떻게 이 코드가 실행되었는지 나온다.  
(gdb) disas main 을 통해 다음과 같은 assembler code가 나타나게 된다.

|물리주소|<main+n>|Instruction by assembly|parameter|
|-|-|-|-|
|0x080482fc|<main+0>:|push|%ebp|
|0x080482fd|<main+1>:|mov|%esp,%ebp|
|...|...|...|...|

이 disasembler code에서 function의 호출에서 당연하게 a=1,b=2,c=3이 순서대로 될 것 같지만,  
assembler로써 보면 push $0x03 push $0x2 push $0x1이 차례대로 실행된다.   
이와 같이 어셈블로러써 의사코드를 확인하는 과정을 리버싱의 기초가 되며, 인자값의 stack 배치 순서가 가장 기본적이며 핵심적인 원리이다.  


# 백도어(Backdoor)
## 정의
컴퓨터 현실에서의 인증을 하려면 아이디와 패스워드를 통해 로그인을 하게된다.  
정상적인 아이디와 패스워드를 입력한 후 서버에서 본인이 맞는지 확인하는 과정을 회피하는 접근 경로를 백도어라한다.

## 종류
1. 로컬 셸 백도어
서버의 셀을 얻어낸 뒤에 관리로 권한 상승을 할 때 사용하는 백도어이다.  
트랩도어 또한 로컬 백도어라 할 수 있다.  
시스템에 로그인한 뒤에 관리자로 권한을 상승시키기 위한 백도어 임으로 로컬 백도어를 이용하기 위해서 공격자는 **일반계정** 하나가 필요하다.

2. 원격 백도어(console)
원격에서 관리자로 계정과 패스워드를 입력하고 로그인한 것처럼 바로 시스템의 관리자 계정으로 로그인 할 수 있는 백도어
**네트워크**에 자신의 포트를 항상 열어놓는 경우가 많다. 일종의 서비스를 제공하는 **데몬**처럼 동작.

3. 원격 GUI 백도어
GUI 환경의 백도어는 대부분 크기가 크고, 많은 데이터를 전송해야 하기 떄문에 노출이 되기 싶다.

4. password craking backdoor
인증을 회피하기 보다는 인증에 필요한 패스워드를 원격지의 공격자에게 보내주는 역할을 하는 백도어  
사용자가 누르는 키보드의 정보를 원격지에 보내는 경우도 있고, 특정 문자열, 즉 'password'와 같은 문자열 뒤에 누르는 키보드의 정보를 원격지에 보내는 경우도 존재

5. 시스템 설정 변경
원격지의 셀을 얻어 낸다는 것 보다는 해커가 원하는대로 시스템의 설정을 변경하기 위한 도구  
유닉스에서 프로그램 스케쥴러로 사용되는 cron 데몬을 사용하는 경우가 많다.

6. 트로이 목마형 
처음부터 백도어를 목적으로 만들어진 프로그램이 아닌데도 백도어로 동작하는 경우  
윈도우에서 웹 브라우저나 명령창, 간단한 게임등도 백도어와 섞을 수 있다.
이렇게 제작된 백도어를 실행하면 원래의 목적의 프로그램이 실행되면서 동시에 백도어도 설치된다.

7. 거짓 업그레이드
이메일을 통해 백도어가 설치된다.

8. 기타
네트워크 데몬/시스템 유틸리티 수정 백도어/TCP,IP 프로토콜을 이용한 shell binding 백도어/ Kernel 모듈을 수정한 백도어/ 방화벽을 우회하는 백도어


## 실습
* id: level1
* pw: level1
* hint: "leve12 권한에 setuid가 걸린 파일을 찾는다"

### 0. 환경 탐색
* public_html/의 디렉토레에서 ls -l 결과, level1 계정에서 읽고 쓸 수 있는 권한이 다음과 같이 표시된다.
> -rw-rw-r-- 1 root level1  1 time  index.html  
* 다음과 같이 user에게 -rw의 권한이 부여가 되어있어서 index.html을 읽고 수정할 수 있다. 그리고 또한 그 이름이 level1 계정에서 가능함을 파악할 수 있다.
* temp/ 에서는 공격에 필요한 소스코드를 작성하거나 메모를 남길 수 있다.

### 1. 문제 분석
* level1과 관련이 있고, level2와도 관계가 있는 백도어를 찾아 level2의 권한을 얻으면 된다는 사실을 추추륵할 수 있다.  
* level2 권한에 setuid를 찾기 위해서는 "find"라는 강력한 system 명령어를 사용하면 된다.
* level2 권한의 백도어란 level2 계정의 소유이고, 다른 계정으로 그룹 권한이 부여돼 있는 SetUID와 SETGUID가 설정돼 있어야 한다는 것이다. 

#### find / -perm +6000 -user level2 -exec ls -l {} \; 2> dev null
* 이때 명령어의 parameter로 / -perm +6000 -user level2 2> /dev/null을 쓰면된다.
* -perm +6000: SetUID와 SETGUID가 둘다 권한이 설정되어있는 것을 찾으라는 것이다.
* -user level2: user의 이름이 level2 인것을 찾으라는 말이다.
* -exec ls -l {} \; : 실행 후 ls -l을 실행하고 그 값을 {}에 넣어서 출력하라는 것이다.
* 2> /dev/null: 나머지 찾기에 오류가 된 값들(권한 오류)는 stderr(2)로 버리라는 뜻이다.

#### 리버싱 [ gdb /bin/ExcuteMe/ ][disas main]
* gdb를 통해 리버싱을 진행하면서 <function_name>을 기준으로 실행흐름을 간단하게 파악해볼 수 있다.
1. stack을 구성한다.
2. system()함수로 명령어를 실행한다. 
3. chdir()함수로 디렉토리를 이동한다.
4. printf()를 통해 문자열 출력한다.
5. fgets() 함수로 사용자로부터 입력을 받는다.
6. strstr() 함수의 반복 사용해 일정 문자열을 비교한다. (문자열 내에서 문자를 찾는다)
7. exit() 함수의 사용으로 문자열이 내부에 있다면 시스템을 종료한다.
8. 없다면 setreuid(3002,3002) 함수를 사용해 **일시적**으로 userid의 권한을 level2로 상승시킨다.
9. 상승된 권한으로 입력받은 문자열을 시스템 함수로 호출시킨다.
#### Comprehence 
* gdb x/s 0x080a0a0a0 를 통해 실제 메모리 주소에 저장된 값을 출력할 수 있다.
* push 뒤의 주소값을 x/s 뒤에 넣는다면 어떤 값을 push 하는지를 파악할 수 있다.
* call 뒤에는 어떤 함수를 호출하는지가 나온다.
* 위의 세가지를 통해 어떤 것을 출력하는지 파악할 수 있다.
* **precedure prelude**란 함수로 진입하면서 함수로 진입하기 전의 스택포인터의 위치를 저장한 다음 지역변수에서 사용할 공간을 스택에 확보하는 과정
main+0 ~ main+6의 과정이다.
* fgets
3가지의 assembly 명령어가 실행된다.
> push1 0x8048a04 : 입력이 저장되는 배열의 주소를 stack에 준다.  
> push $0x1d : 사이즈의 크기를 stack에 준다.   
> lea -40(%ebp),%eax : eax에 ebp-40번지의 주소값을 준다.(주소값 연산)(값을 가져와야하는 경우에 쓰인다)  
> push %eax : eax를 stack에 준다.  
> call <fgets>  
  
#### Memory Architecture(STACK)
* 인자값을 먼저 stack에 올리고(PUSH)
* 마지막으로 지역변수 내의 함수를 호출한다.(CALL)
* 함수가 새 변수를 선언할 때마다 스택에 푸시가 된다.
* 함수가 종료될 때마다 해당 함수에 의해 스택에 푸시된 모든 변수가 해제된다.
* 스택 변수가 해제 되면 다른 스택변수에서도 해당 메모리 영역을 사용할 수 있다.
^ 추가정리..// (컴퓨터 구조에서 stack memory의 위치가 어디일까?)

### 2.문제 해결
* 일시적인 권한을 갖는 파일을 찾기 위해서 find 명령어를 통해 /bin/ExcuteMe/ 를 찾아냈다.
* 실제로 파일을 실행시켜본 결과, setreuid를 통해 level2의 groupId를 일시적으로 부여하는 과정이 있다.
* 이 과정에서 bash/sh/ 등의 셸을 실행하는 명령어를 사용하게되면 지속적으로 level2의 권한을 얻을 수 있다.
* 그 이후 my-pass를 통해 level2의 code를 확인하면 가능하다.

### SetUID, SetGID, Stikybit
* SetUid와 SetGid는 유닉스 시스템에서 파일에 다른 계정과 그룹의 권한을 일시적으로 빌려주는 개념이다.  
* 일반적인 RWX권한과는 다른 독특한 개념이다.  
* chmod 6755 를 통해 setuid와 setguid를 다른 파일에서 실행할 수 있도록 걸어놓으면 
* chmod 1777 를 통해 +t 옵션을 이용하면 디렉토리에 스티키 비트를 설정할 수 있다. => 디렉터리에 지정한 읽고 쓰고 할 수 있는 권한이 있어서 스티키 비트가 설정된 디렉터리의 소유자 계정이 아닌 다른 계정은 해당 디렉터리를 수정하거나 삭제할 수 없다.
* 자신:4 group:2 other:1 로써 stickybit 권한 부여가 가능하다.
