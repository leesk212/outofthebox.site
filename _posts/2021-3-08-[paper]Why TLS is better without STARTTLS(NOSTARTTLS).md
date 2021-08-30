---
tags : 🌟paper-review security-attack email-security
title: '[Paper]Why TLS is better without STARTTLS(NOSTARTTLS)'
date:   2021-08-26 00:19:00 +0900
lastmod : 2017-08-26 12:00:00
sitemap :
changefreq : daily
priority : 1.0
description: >
  python mataplotlib api
toc: True
---

# Insights that I want
* Starttls를 지원하지 않을 때 생길 수 있는 문제점이 무엇일까?
* DNSSEC을 지원하지 않을 때 생길 수 있는 문제점이 무엇일까?
* (Can I?) TLSRPT..!


# Abstract
* We perform the first structed analysis of STARTTLS in SMTP,POP3, and IMAP and introduce *EAST*.
* *EAST*: a semi automatic testing toolkit with more than 100 test cases convering a wide range of variants of STARTTLS stripping, command and response injections, tampering attack, and UI spoofing attacks for email protocols.
* Our analysis focuses on the confidentiality and integrity of email submission(client2MTA)[SMTP] or retrieval(MTA2client)[IMAP,POP3]
* It is very important!!!
* EAST -> analyze 28 email clients and 23 servers --> 40 STARTTLS issue
  * client: 25 ( total 28 ) is vulenrability
  * server: 16 ( total 23 ) is vulnerability
* STARTTLS is error-prone!! --> should avoid!!

# Instructiuon
* STARTTLS is most useful in scenarios where encrpytion is hard to enforce, such as in email relaying running in the back ground without any user interation.
* Email relaying  is often *Oppertunistic* because SMTP servers fall back to plaintexty if a TLS negotiation fails.
* Surprisingly, our analysis showed that some popular email clients use it as default despite having the option to use the implicit TLS ports without STARTTLS.
* Several Issue:
  * STARTTLS stripping attacks : When a Meddler-in-the-Middle (MitM) attacker removes the STARTTLS capability from the server response, they can easily downgrade the connection to plaintext.
  * a command injection bug in Postfix: When a client appends an extra command after the STARTTLS command, that command is buffered and evaluated after the transition to TLS. In effect, this allows an attacker to inject a plaintext prefix into an encrypted session.
  * Trojitá: pre-authenticated connections
* Present systematization of these issues: Negotiation, Buffering, Tampering, Session Fixation, and UI Spoofing

![스크린샷 2021-08-30 오전 4 28 18](https://user-images.githubusercontent.com/67637935/131263002-8607d502-6944-48bd-beda-d1a746271cf4.png)


# Background

## Submission of email
* message submission: the process of introducing a new email to the email infrastructure.
  * MUA(Thunderbird,...)
* message relaying:  the process of forwarding a message as long as it has not arrived at its final destination.
  * Relaying happens after submission, and MUA is not part of that process
  * SMTP
    * Handshake  
      1. Client issues the EHLO command first to obtain a list of server capability.
      2. Server signaled suport for STARTTLS via the STARTTLS capability.
      3. Client starts the transition to TLS via the STARTTLS command.
      4. Client then provides its login credentials to the server(AUTH),(MAIL),(RCTP)
      5. Client finally initiates the transmission of the email's content via the DATA command ".\r\n"
    * Two characteristics of SMTP
      * Every command is answered with exactly one response (+PIPELINING extension)
      * <s>Responses in SMTP cannot be parsed generically but require different parsers depending on the issued command.</s>

## Retrieval of Email
* POP3(Post office Protocol)
  * a simple line-based request and response protocol
  * Allows users to download their email
  * After 1984, POP3 has two siginificant additions to the protocol: 
    1. CAPA Command(the introduction of a mechanism to signal extensions) 
    2. STARTTLS command
* IMAP(Internet Meassage Access Protocol)
  * download and delete protocol
  * Doesn't provide a way to upload messages to a server  
  * ![스크린샷 2021-08-30 오전 5 03 02](https://user-images.githubusercontent.com/67637935/131263850-94dab289-8a06-4529-a2c8-26aa1c3ca000.png)
  * with A tag --> tagged response can be matched regardless of the order they are recieved in
  * unttaged responses begin with a ""*"" and can also be sent while no command is in progress

## STARTTLS and <s>Implicit TLS</s>
* Implicit TLS is distinguished with STARTTLS
  * Submission TLS: 465
  * TLS with POP3: 995
  * TLS with IMAP: 993
* Secure and Performance: Implicit TLS > Explicit TLS(STARTTLS)
* But STARTTLS is default value to an email provider because of not fully supporting implicit TLS.
* However, this is not the case when connecting from a MUA to an MSP.

# Construction of Test Cases
* Our goal aims to find commands or responses a MitM could use against an active SMTP, POP3, IMAP session to obtain sensitive data, or to introduce meaningful changes to a client.

## Well-knwon issue
* MTA to MTA communication
  1. [a command injection attack on SMTP](http://www.postfix.org/CVE-2011-0411.html)
  2. STARTTLS stripping attacks in two variants
  3. a issu with missing discard of capabiliteis
  4. [(Trojita)a conflict with IMAP's PREAUTH greeting](http://jkt.flaska.net/blog/Trojita_0_4_1__a_security_update_for_CVE_2014_2567.html)

## Extension of Well-kwon issues
* extension of 1: cross-protocol attack, which allows hosting HTTPS websites under the certificate of an affected email server.
* extension of 2,3: several more variants exits.

# Attacks
##  Client-Attacks
### Negotiation
#### 1. *NS*: STARTTLS Stripping
![image](https://user-images.githubusercontent.com/67637935/131288122-7cfdb3d9-dfb5-40ab-aaa5-4f5775dcee44.png)

#### 2. *NP*: PREAUTH STARTTLS Blocking
![image](https://user-images.githubusercontent.com/67637935/131288178-2ee80e1f-91c4-40d3-ac4e-30d460e91818.png)
* When a server can preauthenticate a client, it can respond with a PREAUTH greeting.
* In this case, both the client and server must skip authentication and proceed as if the client already logged in.
* 

#### 3. *NR*: Malicious Redirects 
![image](https://user-images.githubusercontent.com/67637935/131288196-8a5d5432-a0fc-40d7-8e7a-3caaa6efb616.png)


### Tampering
#### 4. *TM*: Tampering with the Mailbox
* An attacker can tamper with local mailbox data by sending IMAP's data responses before STARTTLS. 
* IMAP's untagged data responses lead to changes in the mailbox, which can be used for tampering attacks, e.g., placing new messages or folders into the user's mailbox.
* These changes can even lead to a permanently corrupted local state

### UI Spoofing
![image](https://user-images.githubusercontent.com/67637935/131287458-37754e9b-8eb3-46ab-9546-61c9e36ee9ec.png)

#### 5. *UA*: IMAP Alerts
* IMAP alerts are a prime opportunity for UI spoofing.
* Since they can be sent at any point in an IMAP connection, any client is vulnerable to UI spoofing

#### 6. *UE*: Error Messages
* Additionally, all protocols can show error messages that can be sent in response to any command
* If these are displayed in the plaintext phase, UI spoofing is also possible

### Buffering
#### 7. *BR*: Response Injection
![image](https://user-images.githubusercontent.com/67637935/131287939-3e21c2a2-d1e9-4b36-b741-15e88cb43b2f.png)

## Server-Attacks
### Bufferinng
#### 8. *BC*: Command Injection
![image](https://user-images.githubusercontent.com/67637935/131287910-7f0ae0f0-13fa-4594-bce6-68657cee4bab.png)

#### 9. Disclosing Credentials via Command Injection , Breaking Implicit TLS via STARTTLS
![image](https://user-images.githubusercontent.com/67637935/131288498-b65e9f15-b7e1-476b-ae6f-9ce3ae0b330c.png)
![image](https://user-images.githubusercontent.com/67637935/131288598-6e48363d-7976-4a86-8a67-8919f96e7694.png)

#### 10. Hosting HPPS via STARTTLS
![image](https://user-images.githubusercontent.com/67637935/131288639-30e923ec-e6d5-46db-880e-2adf9f3463f8.png)

### Tampering
#### 11. *S*: Sesssion Fixation
![image](https://user-images.githubusercontent.com/67637935/131288671-f6e1db31-7e6a-4b50-8645-fa60694504fb.png)
* If any session data set in the plaintext phase is retained after the transition to TLS, it may allow tampering or information disclosure attacks
* the server allows encrpyted login, and the attacker can authenticate using their account and fixate this seesion for the client (line2, line3)
* The server retains this session through the STARTTLS transition, and the client remains logged into the attacker's account.
* Therefore, thje attacker can now present any mailbox to the client by manipulating their own account
* Additionally, if the client synchronizes any sent or drafted emails to the mailbox, the attacker can retrieve these from their mailbox
 



# Evaluation 
## Client Issues
![image](https://user-images.githubusercontent.com/67637935/131288834-26ffaefb-b65c-4d17-a17b-8f0848cf2abf.png)

## Server Issues
![image](https://user-images.githubusercontent.com/67637935/131288859-d4a0c277-7beb-4a70-bf8b-d07cafbb50d8.png)






