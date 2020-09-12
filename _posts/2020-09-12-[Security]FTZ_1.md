# 메모리 레이아웃 기초

버퍼 오버플로우 공격과 리버싱 같은 시스템 해킹기법을 위해서는 메모리 레이아웃을 반드시 이해해야 한다. 

만약 소스코드가 다음과 같이 있다면 메모리에 배치는 다음과 같이 진행된다.

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


# 백도어(Backdoor란)


