---
tags: assembly computer-architecture
toc: True
---
# 1. Using mfence & lfence
```c
asm __volatile__ (
" mfence \n"
" lfence \n"
);
Your code
asm __volatile__ (
" mfence \n"
" lfence \n"
);
```
* intel CPU에서는 정상적으로 사용이 가능하다.  



# 2. CPUID
```c
 asm volatile(
    "xor %%rax, %%rax\n\t"
    "CPUID\n\t"
    "RDTSCP\n\t"
    "mov %%rdx, %0\n\t"
    "mov %%rax, %1\n\t"
    "mfence\n\t"
    : "=r" (d), "=r" (a)
    :
    : "%rax", "%rbx", "%rcx", "%rdx");
  a = (d<<32) | a;
  ```
  * 시작이 xor 명령어에 대해서 모두다 명령어가 처리되기 전까지 serializing을 하도록 도와준다.
