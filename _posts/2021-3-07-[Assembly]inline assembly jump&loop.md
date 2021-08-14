---
tags: assembly computer-architecture
toc: True
---
```asm
  int i=0,  j=0;
         __asm__ __volatile__(
                  "movl $0, %0  \n\t"
                  "movl $0, %1  \n\t"
           ".Lp1 : "
                   "cmpl $9, %0 \n\t"
                   "jge .End \n\t"
                   "incl %0 \n\t"
        ".Lp2 : "
                   "cmpl $9, %1  \n\t"
                   "jl .Lp4 \n\t"
                   "movl $0,  %1 \n\t"
                   "jmp .Lp1 \n\t"
        ".Lp3 : "
                   "movl  %1, 4(%%esp) \n\t"
                   "movl  %0, (%%esp) \n\t"
                   "call  print \n\t"
                   "movl 4(%%esp), %1 \n\t"
                   "movl (%%esp), %0 \n\t"
                   "jmp .Lp2   \n\t"
        ".Lp4 : "
                   "incl %1   \n\t"
                   "jmp .Lp3 \n\t"
        ".End : "
                   "movl $0,  %0 \n\t"    //
                   :
                   :"r" (i), "r" (j)
          );
```
reference: http://home.konkuk.ac.kr/~cris/xe/?mid=sp_board&document_srl=3203
