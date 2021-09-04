---
permalink: /portfolio/
excerpt: "Minimal Mistakes is a flexible two-column Jekyll theme."
last_modified_at: 2020-09-03T10:15:22-04:00
toc: true
---



<h5> Program: 산학연계 SW프로젝트 </h5>
<h5> Tech Stack: Python Tensorflow Pytorch Matlab PyQt Git</h5>
<h5> Type: Team Project(name: SAILB)&nbsp;</h5><div></div><h5>Subject: 소프트웨어프로젝트 2, 산학협력캡스톤설계1</h5>
<h5> Result: 제5회 산학연계 SW프로젝트 대상(총장상),&nbsp;대한전자공학회 하계학술대회​ 2저자 개제 (논문명: EEG 단체널 기반 Inception 네트워크 순환 신경망을 결합한 자동 수면 단계 분류기)<span id="husky_bookmark_end_1630740390525"></span></h5><h5></h5><br>

<h5> Overview</h5><p>
EEG기반 PSG 데이터를 입력으로 하는 딥러닝 시스템 기반의 5단계 수면단계 예측 시스템</p><p><br>

</p><h6> Architecture<img src="https://user-images.githubusercontent.com/67637935/131971407-e19f7c1b-8676-4562-bf6e-a85b4d3b2153.png" alt="image" style="font-size: 12px; max-width: 100%;"></h6><p>&nbsp;</p><p style="margin-left: 0px;"><span style="font-size: 8.04px;"><b>Layout, Paper&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;</b></span></p><p style="margin-left: 40px;">&nbsp;</p><p>​<img src="https://user-images.githubusercontent.com/67637935/132086417-42982ec3-0f0b-405b-8eae-823692f72d27.png" alt="스크린샷 2021-09-04 오후 4 20 29" style="max-width: 100%;"></p><p>&nbsp;</p><p>&nbsp;</p><div class="attach"><span>영상첨부 </span><div><a href="https://www.youtube.com/watch?v=4lXywrhhnxE" target="_blank"> https://www.youtube.com/watch?v=4lXywrhhnxE</a></div></div>

<p><br><br>

</p><h4>Window PC의 악성탐지 및 모니터링 시스템 개발 ( Sysmon-ELK )&nbsp;</h4>
<span class="data">2020.07 - 2020.08</span><br>
<h5> Program: Kwangwoon University Summer Short-Term Internship </h5>
<h5> Tech Stack: Python PyQt Elasticsearch Logstash Kibana Bash xml winlogbeat Git</h5>
<h5> Type: Personal Project</h5><br>
<h5> Overview </h5><p>
클라이언트(window) 환경에서 발생하는 Event들(Sysmon 로그)을 winlogbeat와 logstash를 통해 서버(ubuntu)로 송신 후 Elasticsearch에서 데이터를 정제, Kibana와 PyQt를 통해 실시간으로 모니터링 및 분석하는 시스템</p><p><br>

</p><h6> Architecture</h6><h6><img src="https://github.com/leesk212/Sysmon-EL-Python_PyQt/raw/master/img/Architecture.png" alt="ex_screenshot" style="font-size: 12px; max-width: 100%;"></h6><h6><br></h6>

<h6> Layout </h6><div><img src="https://github.com/leesk212/Sysmon-EL-Python_PyQt/raw/master/img/layout.png" alt="ex_screenshot" style="max-width: 100%;"></div>

<div class="attach"><span>프로젝트 링크 </span><div><a href=" https://github.com/leesk212/Sysmon-EL-Python_PyQt/" target="_blank"> 
https://github.com/leesk212/Sysmon-EL-Python_PyQt/</a></div></div>

<p><br><br>

</p><h4>머신러닝과 SVM을 활용한 Intel CPU 취약점 공격 탐지 시스템 개발&nbsp;</h4>
<span class="data">2021.03 - 2021.05</span><h5>Program: 4-1 교과 과정</h5><div></div><h5>Title : Microarchitecture Attack Detection Model using Machine Learning</h5><h5>Tech Stack: Python Tensorflow HPC(pcm) Bash Git</h5><h5>Type: Personal Project&nbsp;</h5><h5>Subject: 머신러닝​</h5><div><span style="font-size: 12px;"><span style="font-size: 7pt;"><br></span></span></div><h5>Overview&nbsp;</h5><div>공격자가 피해자 컴퓨터의 비밀 정보를 탈취하는 방법에는 다양한 방법이 있다. 그 중 인텔 CPU의 취약점을 악용한 공격들이 치명적이며 이를 방어 및 예방하기 위해서는 공격들의 기저가 되는 Micro-Architecture Attack (Flush+Reload, Flush+Flush, Meltdown)을 탐지할 수 있어야 한다. 인텔 CPU의 취약점을 목표로 만들어진 공격들이 피해자 컴퓨터 에서 실행될 때 HPC(Hardware performance counter) 상태에 영향을 주며 이는 PCM (Processesor Counter Monitor)을 통해 HPC상태 변화를 확인할 수 있다. PCM으로부터 추출할 수 있고, 기존의 공격 탐지 논문에서 Feature로 사용한 Cache, IPC뿐만이 아니라, Temp, Power등 추가적인 Feature들로 다항식 커널 SVM(Support Vector Machine) 알고리즘을 활용해 기계 학습을 진행한다. 학습 된 모델로 피해자의 컴퓨터가 어떤 Micro-Architecture Attack을 받고 있는지와 평상시 상태를 구분하는 과정을 통해 공격을 예방할 수 있는 모델을 만들어 공격 탐지를 진행한다.&nbsp;&nbsp;</div><div><br></div><div><span style="font-size: 8.04px; font-weight: 700;">Architecture</span>​</div><div><img src="https://user-images.githubusercontent.com/67637935/131985282-2bd4c125-22f3-4b02-b852-005c5087df40.png" alt="2021-09-03_18-40-26" style="max-width: 100%;"></div><div><br></div><div><br></div>

<div class="attach"><span>프로젝트 링크 </span><div><a href="https://github.com/leesk212/Micro-Architecture-Attack-Detection-Model-using-Machine-Learning" target="_blank">https://github.com/leesk212/Micro-Architecture-Attack-Detection-Model-using-Machine-Learning</a></div></div>

<p><br><br>

</p><h4>카카오 로그인 인증을 활용한 학생정보관리 서비스 개발</h4>
<span class="data">2020.09 - 2020.12</span><br>
<h5> Program: 3-2 교과 과정</h5><div></div><h5>Title: Database &amp; Application Project</h5>
<h5> Tech Stack: Python Flask Docker Vue(axios) Javascript</h5>
<h5> Type: Team Project (name: 디비만만) &nbsp;</h5><div></div><h5>Subject: 데이터베이스및응용</h5><div><br></div>

<h5> Overview </h5><p>
학생정보관리 시스템을 주제로 DBMS를 연동하여 웹 애플리케이션을 개발하며 회원 가입 및 카카오 계정 연동, 개인 정보 조회, 강의 시간표 조회, 수강신청 기능, 과목별 공지사항 게시판, 학습결과(수강/성적 조회) 등의 기능들을 기본으로 포함한다.</p><p><br>​



</p><h6> My Backend Work</h6>

- 카카오데이터서버 연동 및 어플리케이션 개발, 개인code갱신기능구현, Access token을 통해 사용자 개인 정보 (email, 프로필 사진, 아이디, 성별 등) 갱신 기능 구현 <p>

- API (Flask 개발):  학생정보조회 및 검색 api 구현, 댓글관련 api 구현, 과목조회관련 api 구현</p><h6> My Frontend Work</h6>

-  Author Page 구현 <p>

- Login , FindPW, NewAccount Page 버튼 기능 연동 </p><p>

- 세션 유지 및 kakao 정보 쿠키화, Enrollment Page 버튼 기능 구현,과목 즐겨찾기 CRUD, 즐겨찾기 된 class 개인 등록 기능, Notice Page 버튼 기능 구현 </p><p>

- Axios: Login page 관련 kakao 데이터 센터 연동 구현, 중복 email 확인 기능 api 연동, Enrollment page 관련 class api 연동, Notice page 관련 post api 연동, Main page 관련 timetable api 연동 

</p><p>&nbsp;</p><p>&nbsp;</p><h6>Architecture</h6><h6><img src="https://user-images.githubusercontent.com/67637935/132084610-a00e56e3-b2ce-40e1-bb46-6856b4770f5c.png" alt="스크린샷 2021-09-04 오후 3 04 37" style="max-width: 100%;"></h6><h6><br></h6>



<div class="attach"><span>프로젝트 링크 </span><div><a href="https://github.com/0xF4D3C0D3/kw-db-project-2020" target="_blank">https://github.com/0xF4D3C0D3/kw-db-project-2020</a></div><div><br></div></div><div class="attach"><span>영상첨부</span><div><a href="https://www.youtube.com/watch?v=4eEvMKFw9_g&amp;t=332s" target="_blank">https://www.youtube.com/watch?v=4eEvMKFw9_g&amp;t=332s</a></div><div><br></div><div><br></div></div><br>


<p><br><br>


</p><h4>Meltdown-type Attack을 활용한 Intel CPU 취약성​의 정량적 분석 시스템 개발</h4>

<span class="data">2021.01 - 2021.08</span><br>

<h5> Program: Undergraduate Research Student </h5><div></div><h5>Title: Quantitative Analysis on Attack Capacity in Meltdown-type Attacks</h5>

<h5> Tech Stack: Python C Mips bash gdb gdb-peda ubuntu-kernel </h5>

<h5> Type: Personal Project  &nbsp;</h5>

<h5> Result: 국제 학술대회 발표 및 Springer(LNCS) 1저자 논문 게재</h5>

<h6>- Conference: WISA (The World Conference on Information Security Application) </h6><h5><br></h5><h5> Overview </h5><p>

In recent years, modern CPUs have been suffering from Meltdown-type attacks. These attacks are delivered by exploiting transient execution created by a faulting load operation. A secret value is encoded into the cache by transient instructions, which in turn is deduced from a microarchitectural covert channel such as Flush+Reload. Recent studies on these attacks mainly focus on finding new vulnerable microarchitec- tural structures, while lacking interest in how many transient instruc- tions can be executed in the transient execution. If attackers know the exact attack capacity, i.e., the maximum number of instructions avail- able within a transient execution window, they will be able to maximize information leakage by executing additional transient instructions. In order to devise security solutions against Meltdown-type attacks, it is of crucial importance to measure and evaluate the attack capacity. In this paper, we quantitatively analyze the attack capacity in terms of the number of μops, the latency of transient instructions, and the size of the Reorder Buffer (ROB). Specifically, we present our method in detail that measures the capacity by reconstructing the original implementations of Meltdown-type attacks. We analyze the attack capacity by conducting experiments with various CPU models and identify several elements that affect the capacity. Based on our findings, we propose two methods that reinforce the Meltdown-type attacks.​</p><p>&nbsp;</p><p>&nbsp;</p><p><img src="https://user-images.githubusercontent.com/67637935/132085898-1720f624-7924-4632-965b-7e492a25fa5f.png" alt="스크린샷 2021-09-04 오후 4 01 42" style="max-width: 100%;"></p>

















<!--classes: wide!-- 메뉴바가 상단 고정됌> 

<!--![ex_screenshot](../assets/images/teaser.png) -->
<!-- 
## **ABOUT ME**
> <img src="../assets/images/b-removebg-preview.png" alt="drawing" width="200"/>
>  
> - Age: 23  
> - Name: 이석민 (Lee SeokMin)  
> - Address:  Dongdaemun-gu, Seoul, Republic of Korea
> - Email : leesk212@gmail.com or leesk212@naver.com

Educational History

 > Bachelor 
 >> **Kwangwoon University**(Nowon-gu, Seoul)  
 >> College of Software and Convergence  
 >>> - Major: Computer information engineering  
 >>> - Linked Major: BigData  
 >>> - Status: Junior  
 >>> - Term: 2016/03 ~ 
 -->

<!-- Work Experience

> InTheForest(Cyber Security Company) 
>> - Program: Kwangwoon University Summer Short-Term Internship      
>> - Project experience: Sysmon-EL-Python_PyQt
>> - Term: 2020/07/03 ~ 2020/08/25
>  
> [CSS Lab(Compuer Systems Security Lab)](https://sites.google.com/view/icseclab/home)
>> - Program: Korea University Undergaduate reasearch student      
>> - Project experience: Ongoing
>> - Term: 2020/09/07 ~ 

Languages
> - C/C++:  ⭐⭐⭐⭐  
> - Python: ⭐⭐⭐  
> - JAVA:   ⭐⭐  
> - MIPS:   ⭐⭐
> - Matlab: ⭐
> - Elasticsearch: ⭐⭐⭐   
> - SQL    
>> - MySQL:   ⭐⭐⭐

Military service status  
> - Airforce ETS(Expiration Term of Service)

## **INTERESTS**
> - Security  
> - Big Data Processing  
> - AI   

## **PROJECTS**
> Sysmon - EL - Python_PyQt  
>> - status: Public  
>> - detail: Sysmon logs in the window environment are received from a computer in another environment through winlogbeat through Logstash, and then repositioned in Elasticsearch and displayed in PyQt.  
>> - github link: https://github.com/leesk212/Sysmon-EL-Python_PyQt  
>  
> DeepSleepNet  
>> - status: Private
>> - detail: Improved Accuracy of Sleep Stage

## **CERTIFICATE**

> Nationally recognized certification  
>> - Industrial Engineer Information Processing  
(Human Resources Development Service of Korea)
>> - Craftsman Photography  
(Human Resources Development Service of Korea)
>> - Linux Master Level 2 1st pass  
(Korea Association for ICT promotion)  
>  
> Language  
>> - TOEIC Score 825  
(ETS)
>> - TEPS Score 283(Level 3+)  
(Seoul National University Language Education Center)
>> - TOEIC Speaking Score 120(Level 5)  
(ETS)

## **EXTRACURRICULAR EXPERIENCE**

 -->
