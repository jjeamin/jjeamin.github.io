---
layout: post
title:  "Google Assistant Robot"
summary: "Google Assistant Robot 만들기"
date:   2019-07-09 13:00 -0400
categories: pi
---

# Google Assistant 설정하기

- 이곳 : [Here]()

# Google Assistant를 이용한 로봇
- IFTTT
- Google Assistant
- Raspberry Pi 3B+

# IFTTT
- If This Then That : 서로를 연동시켜주는 서비스

```
라즈베리파이(GPIO)  <->  Google Assistant
Arduino            <->  Google Assistant
```

# Method

- [https://www.instructables.com/id/Wi-Fi-Voice-Controlled-Robot-Using-Wemos-D1-ESP826/](https://www.instructables.com/id/Wi-Fi-Voice-Controlled-Robot-Using-Wemos-D1-ESP826/)
- [https://m.blog.naver.com/PostView.nhn?blogId=cosmosjs&logNo=221110517520&proxyReferer=https%3A%2F%2Fwww.google.com%2F](https://m.blog.naver.com/PostView.nhn?blogId=cosmosjs&logNo=221110517520&proxyReferer=https%3A%2F%2Fwww.google.com%2F)

## IFTTT 설정하기
- this : `google assistant`
- that : `webhook`

google assistant에 명령어가 온다면 라즈베리파이가 web request(REST API)를 받아서 그에 해당하는 동작을 실행 시킨다.

## REST API
- URL
- HTTP METHOD(GET, REQUEST, PUT, DELETE)

## 간단한 시나리오

```
Go forward : 앞으로 가
Go back    : 뒤로 가
Go right   : 오른쪽으로 가
Go left    : 왼쪽으로 가
```
