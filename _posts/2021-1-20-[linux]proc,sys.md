---
tags: linux
toc: True
---
1. /proc 디렉토리의 프로세스 파일의 상세 설명

 

- What is a Process?

           프로세스는 실행중인 프로그램

           프로세스와 연관된 항목

                           • process ID (PID)

                           • priority

                           • nice value

                           • memory

                           • environment

                           • file handles

                           • exit status

 

- Process Lifecycle

           프로세스는 계층구조를 가짐

             • init – 커널이 실행한 첫 번째 프로세스. PID:1

                           커널이 유일하게 실행시킨 프로세스

                           init은 자식프로세스를 실행시킴

                           child processes는 또 다른 자식 프로세스를 실행함

 

           Processes는 두 가지 단계를 통해 프로세스를 생성함

                     • fork() – 자신과 동일한 프로세스 복제

                           • exec() – 부모를 대체해서 완전한 자식 프로세스가 실행

                           • fork() + exec() – 다른 프로세스 구동 시 호출되는 system call

 

           Process 종료 방법

                           • exit()를 통한 정상 종료

                           • abort()를 통한 비정상 종료

 

- Process States

           프로세스는 신호를 받는 즉시 상태 전환이 가능

           running ⇒ 현재 구동중인 상태

             stopped ⇒ 메모리에 load되 있으나, 실행 중이지는 않음

             sleeping ⇒ user input과 같은 특별한 event를 기다리는 상태

             un-interruptible sleep ⇒ 이름에서도 알 수 있듯이 I/O 대기 상태

             zombie ⇒ PID와 exit 상태를 제외한 모든 resource를 반환한 상태

 

- Linux application virtual memory map


 

Text : if, for, while과 같은 일반 프로그램 code가 저장되어지는 영역. 0x00400000

Initialized data : ini i=0;과 같이 초기값이 존재하는 변수가 저장되는 영역

Uninitialized data(bss) : int I; 과 같이 초기값이 존재하지 않는 변수가 저장되는 영역

Heap : malloc()과 같은 라이브러리를 통해 메모리를 동적으로 할당 받을 때 사용되는 공간

Shared Library : 공유 라이브러리가 저장되는 영역.   0x7f15a0d12000

Stack : 함수 호출 시 호출한 함수의 정보를 저장해두는 영역. 7fffa9ee000

Command line arguments : command-line에서 입력한 아규먼트(argv)를 저장하는 영역

Environment variable : 프로세스가 구동되는 데 필요로 하는 환경변수가 저장되는 영역

 

- /proc 프로세스 정보

/proc/PID/ 디렉토리는 현재 구동중인 프로세스의 정보를 기록

# man 5 proc

 

/proc/1          init 프로세스의 세부 정보를 저장

 

/proc/self       현재 실행중인 프로세스의 디렉토리 표시

             # ls -l /proc/self         ⇒ ls 프로세스에 대한 프로세스 디렉토리를 가리키는 링크

# cd /proc/self              ⇒ 로그인 shell(bash)의 /proc 디렉토리로 이동

             # cat cmdline

             -bash

             # ls -l 

              파일의 사이즈가 모두 0으로 보이는 이유는 각 파일이 기본적으로 커널 자료구조를

 들여다 보는 창이며 따라서 진짜 파일이 아니라 아주 특수한 유형의 파일이기 때문

 

/proc/<pid>/maps

 : 프로세스가 mapping된 메모리 주소 공간을 보여줌.

모든 프로세스에는 각자 주소 공간이 있으며, 이 주소 공간은 가상 메모리 관리자

(Virtual Memory Manager)가 제공하고 관리한다.

 

           # cat maps

00400000-004b3000           r-xp      00000000  08:02   12525598          /bin/bash

006b2000-006b3000           r--p      00000000  08:02   12525598          /bin/bash

006b3000-006bd000           rw-p     00000000  08:02   12525598          /bin/bash

 

006bd000-00717000           rw-p      006bd000  08:02   0                     [heap]

 

7f15a0d12000-7f15a0d1c000 rw-p 00000000 08:02     4038836 /lib64/libns..

…

7fffa9ee000-7fffa9f3000       rw-p      7ffffffea000           00:00     0                         [stack]

7fffa9fff000-7fffaa00000       r-xp       7fffa9fff000           00:00     0                         [vdso]

ffffffffff600000-ffffffffff601000 r-xp  00000000 00:00 0                    [vsyscall]

 

   

00400000-004b3000           프로세스가 차지하는 메모리 주소공간

r-xp     mapping에 대한 퍼미션. 이 매핑은 내부용(private)이며 read와 execute가 가능하다는 의미

00000000 파일의 내부 offset

08:02                    device number(Major:Minor)

12525598 inode number

 

디바이스 Major number 8을 사용하는 장치의 2번째 파티션에 bash 프로그램은 위치하며, inode number 12525598을 사용하며 메모리 주소공간 00400000-004b3000에 매핑되어 읽기와 쓰기가 가능한 상태이다.

 

             # ls –il /bin/bash

             # ls –l /dev/sda2

 

 

/proc/<pid>/cmdline

             프로세스 인수(argv) 전체를 포함. Command line에서 넘어온 argument를 포함하여 프로세스가 실행된 방식을 정확하고 신속하게 파악하는 수단으로 사용.

 

/proc/<pid>/coredump_filter

             메모리 유형의 비트마스크를 포함하며

프로세스의 어떤 메모리 세그먼트를 덤프시킬것인지 설정

다음과 같은 메모리 유형이 지원

                           bit 0 — 무명의 비 공유 메모리

                           bit 1 — 무명의 공유 메모리

                           bit 2 — 'file-backed' 비공유 메모리

                           bit 3 — 'file-backed' 공유 메모리

비트마스크가 설정되면, 해당 메모리 유형의 메모리 세그먼트는 덤프됨

            

             # cat coredump_filter

             00000023

 

/proc/<pid>/cwd

             프로세스가 사용중인 디렉토리나 파일

             # ls –l

 

/proc/<pid>/environ

             프로세스의 현재 환경을 저장. 프로세스 map에서 가장 아랫부분,

즉 커널이 프로세스 환경 정보를 저장하는 메모리 위치를 직접 가리키는 링크이다.

             프로그램 실행 중 환경 변수 설정을 알고 싶을 때 이 파일을 확인

             # cat environ

             CONSOLE=/dev/consoleTREM=linux…PATH=/usr/penta_np:/sbin…

 

/proc/<pid>/exe

             실행중인 프로그램 이름

             # ls –l

 

/proc/<pid>/fd

/proc/<pid>/fdinfo

             프로세스가 사용중인 file descriptor 링크와 정보를 저장

 

/proc/<pid>/limits

             프로세스에 적용된 resource 제한사항

 

/proc/<pid>/loginuid

             해당 프로세스를 실행하는 login uid

 

/proc/<pid>/mem

             프로세스가 사용중인 메모리 상태

 

/proc/<pid>/mountinfo

/proc/<pid>/mounts

/proc/<pid>/mountstats

             프로세스에 전달된 시스템의 장치 마운트 상태

 

/proc/<pid>/net

             프로세스에 전달된 시스템의 네트워크 정보

             # cd net

             # cat netstat

             # cat route

             # cat tcp

             # cat udp

             # cat arp

 

/proc/<pid>/oom_adj

/proc/<pid>/oom_score

             linux는  virtual mamory mapping 기술을 사용하기 때문에 overcommit,

즉, 응용프로그램이 사용하는 가상주소의 공간이 실제 물리 메모리보다 크게

할당될 수 있음.

응용프로그램이 실제 존재하지 않는 메모리 공간에 접근할 때 커널은  Out-Of-Memory Killer를 실행하여 해당 응용프로그램을 강제로 종료시킴.

 

OOM score는 OOM 상황에서 이 프로세스가 종료될 가능성의 정도를 결정하고,

OOM 스코어의 값이 다른 프로세스보다 높다면 oomkill에 의해서 종료될 가능성이 더 크게 된다.

oom-adj는  oom_score값을 변경할 때 사용한다.

 

 

/proc/<pid>/pagemap

             프로세스가 사용중인 실제 물리 메모리에 대한 page 정보

 

/proc/<pid>/root

             프로세스가 실행시 적용되는 실제 root(/) 디렉토리

             # ls -l

 

/proc/<pid>/sched

             프로세스에 적용된 CPU 스케쥴링 정보를 저장.

이 정보를 이용해 전체 task 스케쥴링을 진행함.

# cat sched

 

/proc/<pid>/sessionid

             프로세스의 세션 ID

             # cat sessionid

 

/proc/<pid>/smaps

             Virtual memory map을 좀더 자세히 보여줌.

             # more smaps

 

/proc/<pid>/stat

             해당 프로세스에 대한 정보를 기록

프로세스 id, process name, 상태, 부모 프로세스 id 등

 

/proc/<pid>/statm

             메모리 사용에 대한 정보를 제공한다.

size       : 전체 프로그램의 사이즈

resident   : swap되지 않고 설정된 사이즈

share      : 공유 페이지

text       : 텍스트 (코드) 사이즈

lib         : 라이브러리 사이즈(Linux 2.6 에서는 사용되지 않는다.)

data      :  data와 stack을 합한 사이즈

dt         : dirty pages (Linux 2.6 에서는 사용되지 않는다.)

 

# cat statm

 

/proc/<pid>/status

             Stat, statm에서 확인한 모든 프로세스와 메모리 정보를 좀더 쉽게 알아 보기 쉽게 표현

        Name: 프로세스가 실행된 명령어 이름
        State: 프로세스의 현재 상태

        Tgid: 스레드 GROUP ID 또는 PID
        Pid: 스레드 ID.
        TracerPid: 프로세스 추척을 위한 PID
        Uid, Gid: 실제로 파일시스템에 저장된 UID, GID.
        FDSize: 현재 할당되어 있는 file descriptor 슬롯의 갯수
        Groups: group 리스트의 보조 정보
        VmPeak: 최고로 할당된 가상메모리 크기
        VmSize: 가상 메모리 크기
        VmLck: lock된 메모리 크기
        VmHWM: swap되지 않고 설정된 가장 큰 크기.
        VmRSS: swap되지 않고 설정된 크기.
        VmData, VmStk, VmExe: data, stack, text 세그먼트의 크기.
        VmLib: 공유라이브러리 code의 크기.
        VmPTE: 페이지 테이블 엔트리 크기.
        Threads: 프로세스에 포함된 스래드들의 개수.
             …

 

/proc/<pid>/task

             프로세스의 스레드 정보를 기록한다.

 

/proc/<pid>/wchan

             프로세스가 wait하고 기다리고 있는 동안의 "channel" 값

 

 

- /proc Kernel parameter 튜닝

/proc/sys/ : 조정 가능한 커널 매개변수를 저장

• view current values with cat

• modify with echo

• view and modify with sysctl command

/etc/sysctl.conf  : kernel parameter를 영구저장

 

 

 

- /proc 시스템 정보

/proc/cmdline    부트 로더에서 넘어온 커널 아규먼트 정보

/proc/cpuinfo    프로세서 정보. CPU 타입, 모델, 제조사 등

/proc/devices    현재 로드된 디바이스정보

/proc/fb                          frame buffer 정보

/proc/filesystems            지원하는 filesystem

/proc/interrupts              장치가 사용중인 인터럽트(IRQ) 목록 표시

/proc/iomem      Memory Map

/proc/ioports     현재 사용중인 Input/Output 포트

/proc/kallsyms 커널 심볼정보

/proc/loadavg    시스템의 평균 부하량. 1,5,15분통계

/proc/meminfo   메모리 정보 - free해서 나오는 것 보다 자세함

/proc/misc                      misc device 정보를 출력. Major 10, character device.

/proc/mounts    device 마운트 정보

/proc/partitions 파티션 정보

/proc/stat                        시스템 상태에 관한 다양한 정보.

CPU 사용통계, 부팅 이후 page fault 발생횟수 등

/proc/swaps                   스왑 파티션의 크기와 사용량

/proc/uptime      시스템이 얼마나 살아있었는지에 대한 정보.

/proc/version    리눅스 커널 버전 정보

 

 

2. /sys 디렉토리

- sysfs filesystem의  mount 장소.

- 시스템의 모든 장치에 대한 항목을 포함하고 있음

 

a. Main sysfs directories

• /sys/block/                   블럭디바이스 정보를 기록

• /sys/bus/                     특정 버스에 연결된 모든 device 정보를 기록

• /sys/class/                   기능에 따라 device를 분류해서 정보기록

             ex. /sys/class/net : 각각의 network interface card 정보

                  /sys/class/input : 모든 input device에 대한 정보(keyboard, mice...)

• /sys/devices/               device 연결상태에 대한 계층구조

• /sys/module/                커널 모듈 디렉토리

 

b. Other sysfs directories

• /sys/firmware/

• /sys/kernel/                  /sys/kernel/debug : 디버깅을 위해 커널 및 드라이버 개발자가 사용

• /sys/power/                  이 디렉토리를 통해  system 전원 상태를 제어 .



# Reference: http://egloos.zum.com/powerenter/v/10949008
