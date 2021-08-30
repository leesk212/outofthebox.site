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
#### *NS*: STARTTLS Stripping
#### *NR*: Malicious Redirects 
### Tampering
#### *TM*: Tampering with the Mailbox
### UI Spoofing
#### *UA*: IMAP Alerts
#### *UE*: Error Messages
### Buffering
#### *BR*: Response Injection
## Server-Attacks
### Bufferinng
#### *BC*: Command Injection
#### Disclosing Credentials via Command Injection
#### Breaking Implicit TLS via STARTTLS
#### Hosting HPPS via STARTTLS
### *S*: Sesssion Fixation











