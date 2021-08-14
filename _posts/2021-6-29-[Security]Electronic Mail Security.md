---
tags: network email-security security-defense
toc: True
---
# Policy
## SPF
![image](https://user-images.githubusercontent.com/67637935/125036408-ee8bf800-e0cd-11eb-8c30-5c9c13824b4a.png)
*  google.com로부터 메일이 발송되었을 때, 이 발송지가 진짜 google.com으로부터 발송된 메일인지 확인하는 시스템
  
*  수신자 입장에서 1.2.3.4 ip를 가진 bounces@example.com으로부터 메일이 도착함

* Return Path(반송처: 답장이 도착하는 주소)는 example.com임

* Example.com에 1.2.3.4에게 자신의 도메인(example.com)을  사용할 수 있는지 SPF record에 1.2.3.4가 등록되어 있는지 검증 진행

>Publish DNS TXT Record policy:
example.com IN TXT “v=spf1 +mx +a:cole.xample.com/28 –all”

## DKIM
![image](https://user-images.githubusercontent.com/67637935/125036610-272bd180-e0ce-11eb-9c79-81de8cf8f371.png)

* 메일이 전송 중에 다른 사람에 의해서 변조되지 않았는지를 검증하는 절차

* 공개키/사설키를 활용한 검증 시스템

* 송신 도메인에서 메일을 발송할 때, 발송 서버는 사설키로 해쉬 값을 만들고 이를 헤더에 넣어 발송

* 메일 수신 서버가 메일을 받으면 발송자의 도메인의 DNS에 있는 공개키로 복호화 진행

* 복호화한 해시값을 확인하여 메일이 중간에 변조되었는지를 확인함

>Publish Email header:
DKIM-Signature: v=1; a=rsa-sha256; d=example.com; s=toast; t=1117574938; x=1118006938; h=from:to:subject:date; bh=MTNDU …=; b=dzOd…

>Publish DNS TXT Record policy:
example.com IN TXT “v=DKIM1;k=rsa;p=MIGfMA0…

## DMARC
![image](https://user-images.githubusercontent.com/67637935/125036643-30b53980-e0ce-11eb-8d27-2290adf8e34f.png)
* SPF 와 DKIM에 Reporting기능을 추가하여 진행

* SPF와 DKIM은 단점들을 보안하여 둘 사용해 Reporting진행
	(SPF는 도메인에 대한 검증만, DKIM은 내용에 대한 검증만)

* Publish DNS TXT Record policy:
example.com IN TXT "v=DMARC1;p=none;sp=quarantine;pct=100;rua=mailto:dmarcreports@example.com;"

## MTA-STS

![image](https://user-images.githubusercontent.com/67637935/125036782-58a49d00-e0ce-11eb-94a0-41f831f61c7c.png)

![image](https://user-images.githubusercontent.com/67637935/125036767-54787f80-e0ce-11eb-8f11-03f583893a99.png)

![image](https://user-images.githubusercontent.com/67637935/125036754-517d8f00-e0ce-11eb-8d2a-a2cb61a2ef1b.png)

![image](https://user-images.githubusercontent.com/67637935/125036745-4f1b3500-e0ce-11eb-84ef-884ec1a8e895.png)

* 송신 및 수신 메일 서버(MTA) 간에 암호화/보안 계층을 추가하도록 설계된 inbound mail protocol

* 수신자의 DNS record에 MTA-STS policy를 계시할 경우, 송신자에서도 MTA-STS policy가 계시가 되어있어야 TLS 연결을 통해서만 통신지원

* SMTP 세션의 일부 ( “250 STARTTLS” )를 제거할 수 있는 공격자를 통해 통신이 암호화가 해제되는 것을 막는 것을 목표로 하는 시스템

* DNS를 Third party로부터 가져와 연결을 확인하는 것을 통해 수행됨

* 정의된 하위 도메인에서 HTTPS를 통해 정책 파일을 가져오도록 메일 서버에 지시하는 DNS Record를 사용하여 작동

* DNS와 HTTPS의 조합을 사용하여 암호화된 채널을 사용할 수 없을 때 사용자에게 알려주는 시스템

> Publish DNS TXT Record policy: 
	_mta-sts.example.org.  IN TXT “v=STSv1; id=202104012135;”

> Publish HTTPS policy: 
	https://mta-sts.mail.ru/.well-known/mta-sts.txt
  
  
## SMTP TLSRPT
![image](https://user-images.githubusercontent.com/67637935/125036897-7ffb6a00-e0ce-11eb-8a27-948c55ee03f8.png)
