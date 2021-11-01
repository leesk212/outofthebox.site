---
tags: linux remote_connect ssh gui
toc: True
---

> ref: https://jimnong.tistory.com/748

# Prepare
* 기본base: putty가 깔려있어야함

# Server configuaration
> $ sudo apt-get install xauth

> $ sudo vim /etc/ssh/sshd_config

> config file 내부에 있는 X11Forwarding yes로 수정


# Client Install
> https://mobaxterm.mobatek.net/

설치 후 putty session으로 그대로 접속하면 gui 뜨기 가능

![image](https://user-images.githubusercontent.com/67637935/139657174-10933ce9-009d-45e5-b04b-b086ed245a4a.png)
