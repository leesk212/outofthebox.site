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
      *  



















