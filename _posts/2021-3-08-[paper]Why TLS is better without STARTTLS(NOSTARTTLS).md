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






















