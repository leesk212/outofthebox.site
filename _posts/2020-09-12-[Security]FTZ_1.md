# 메모리 레이아웃 기초

버퍼 오버플로우 공격과 리버싱 같은 시스템 해킹기법을 위해서는 메모리 레이아웃을 반드시 이해해야 한다. 

만약 소스코드가 다음과 같이 있다면 메모리에 배치는 다음과 같이 진행된다.

```
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

물리적주소|메모리 세그먼트|저장 데이터|실제데이터|
0x000000|Text
