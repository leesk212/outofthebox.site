---
tags : git
title: '[git]gitblog-google-노출시키기'
date:   2021-08-22 00:19:00 +0900
lastmod : 2021-08-22 12:00:00
sitemap :
changefreq : daily
priority : 1.0
toc: True
---
# 기본적인 노출방법
1. [git계정이름].github.io repository root위치에 2가지 파일(robots.txt,sitemap.xml)을 추가시켜준다.
  * ref: <https://moon9342.github.io/jekyll-sitemap>
2. google search console로 접속하여 해당되는 sitemap속성으로 이동후 자신의 블로그 + sitemap.xml 
![스크린샷 2021-08-22 오후 2 21 43](https://user-images.githubusercontent.com/67637935/130343414-d89f1b46-6efe-4da1-be87-292d49780875.png)
3. 오류확인..!

# 평범한 시작.. 하지만 sitemap 접근에서의 계속되는 오류..
* 오류: error on line 20 at column 69: EntityRef: expecting ';'
![스크린샷 2021-08-22 오후 2 12 45](https://user-images.githubusercontent.com/67637935/130343242-1af01c7e-c979-4d1d-83b0-a718d501e708.png)

![스크린샷 2021-08-22 오후 2 07 05](https://user-images.githubusercontent.com/67637935/130343083-96d7d9bc-f882-400c-a994-bdee92415a1a.png)

> 너무 화가났다.. 역시 네트워크 관련된건 다 날 왜 화나게 할까..? 아무리 sitemap을 수정하고 잡아도 오류가 잡히지 않았다.

> 문제는 내 포스팅에 적혀있는 공백들과 '&' 문자였다...
> 그것들을 잡아주고 나니

![스크린샷 2021-08-22 오후 2 08 20](https://user-images.githubusercontent.com/67637935/130343113-18b71eb7-8ea7-47bf-87ab-7707df526567.png)

> 성공적으로 sitemap이 모두 열리게 되었다.

# 두근두근.. sitemaps 제출 확인..!
21/08/22.. 계속 같은 오류다.. 혹시나하고 구글링을 해보니, 오류를 수정해주기 전까지 약간의 시간이 필요하다고 한다.. (정말이겠지..?)
