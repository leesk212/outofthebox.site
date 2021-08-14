---
tags: linux security-defense
toc: True
---
# sudo vim /etc/default/grub 확인

# GRUB_CMDLINE_LINUX_DEFAULT="off할 명령어들"
> nokaslr 추가
> pti=off 추가  

![image](https://user-images.githubusercontent.com/67637935/116195666-7cae3e80-a76d-11eb-94ae-49dca206c556.png)

# update시키기
> $ sudo upate-grub
> $ sudo reboot
 

# KPTI disabled 적용확인
> $ cat /proc/cpuinfo | grep pti

pti가 on 되어있면 flag에 pti가 뜬다.

## off 되어 있는 상태
![image](https://user-images.githubusercontent.com/67637935/116196598-ac117b00-a76e-11eb-99ce-bbcc1008e751.png)
