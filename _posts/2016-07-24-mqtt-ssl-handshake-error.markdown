---
author: gavin
comments: true
date: 2016-07-24 22:17:09
layout: post
title: mqtt传输加密ssl握手失败
categories:
- Java
- Android
---

前几天在接入mqtt的传输加密时，使用5.1系统的手机测试通过，但在使用一款4.2.2系统的平板时，遇到了个一异常：

	handshake unkonw error

当时第一反应就是Cipher Suites不支持，于是

	openssl s_client -connect testim.weibangong.com:8883 -showcerts

结果看到

	New, TLSv1/SSLv3, Cipher is ECDHE-RSA-AES256-GCM-SHA384

而GCM Cipher在Android5.0以上才支持，所以问题就很明确了