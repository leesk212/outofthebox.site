---
tags: linux remote-connec 
toc: True
---
> 삽질을 너무 많이 했다..

> ref: <https://velog.io/@hayaseleu/%EB%A6%AC%EB%88%85%EC%8A%A4%EC%97%90%EC%84%9C-%ED%81%AC%EB%A1%AC-%EC%9B%90%EA%B2%A9-%EB%8D%B0%EC%8A%A4%ED%81%AC%ED%86%B1-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0>

# ubuntu chrome 설치
* 예전과 달리(key page추가 없이) firefox에서 <www.google.com/chrome/> 접속 후 gui로 설치하면 된다. 
# chrome remote-computer
* 크롬 원격 데스크톱 으로 접근 후 설치해주면 된다. 아마 deb파일이 있다면 정상적으로 설치가 잘 될 것이다.
# session 유지 위한 home directory에서 chrome-remote-desktop-session 파일 있는지 확인
* 없다면 
  * /usr/share/xsessions 에서 각자의 .desktop 파일을 연다.
  * .desktop 파일에서 Exec= 행의 gnome-session으로 시작하는 부분부터 끝까지를 복사한다.
  *  touch .chrome-remote-desktop-session
  *  exec /etc/X11/Xsession 'gnome-session --session=ubuntu' 적고 저장
* 여기까지 잘 되었다면 다른 컴퓨터에서 linux접근을 하고자 할때 정상적으로 linux pc의 이름이 보이게 될 것 이다.
# 검은 화면.. 해결!
* sudo usermod -a -G chrome-remote-desktop 계정이름(hostname) 
  * 만약 안되면 sudo groupadd chrome-remote-desktop 실행시켜주면 된다.
* /opt/google/chrome-remote-desktop/chrome-remote-desktop --stop (조심해라 이부분에서 2번 os 밀었다.)(이왕이면 직접 gui로 꺼주는 것을 추천)
* sudo gedit /opt/google/chrome-remote-desktop/chrome-remote-desktop
* ![image](https://user-images.githubusercontent.com/67637935/132165395-760cffcd-a772-4c2f-acf5-5afce8734fd1.png) (정확하게 다 해야한다)
* /opt/google/chrome-remote-desktop/chrome-remote-desktop --start

# 생각
자동으로 session을 잡아주는 코드를 적어준 듯하다. 기존의 코드는 해당되는 서버의 세션을 잡아주는데, 그 과정에서 문제가 있으니, 직접적으로 출력해줄 디스플레이를 설정하여 적어준듯 하다.
