---
tags: linux ssh remote-connect
toc: True
---
# open-sshd가 설치 되어 있어야함
# 없다면
> $ sudo apt-get install open-sshd    
> $ sudo service ssh start    
> $ sudo service ssh status    

# 접근 명령어
> $ ssh hostname@ip_address -p port_number
