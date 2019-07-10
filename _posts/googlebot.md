---
layout: post
title:  "Google Assistant Robot"
summary: "Google Assistant Robot 만들기"
date:   2019-07-09 13:00 -0400
categories: pi
---

# Google Assistant Robot
Google Assistant와 IFTTT를 이용해 명령하며 동작하는 Robot 만들기

# Google Assistant API 설정하기

**참조** : [Here](https://jjeamin.github.io/pi/2019/07/09/googleapi/)

# Dependency
- IFTTT
- Google Assistant
- Raspberry Pi 3B+

---

# IFTTT
- If This Then That : 서로를 연동시켜주는 서비스

```
라즈베리파이(GPIO)  <->  Google Assistant
Arduino            <->  Google Assistant
```

# IFTTT 설정하기
- This : `google assistant`
- That : `webhook`

google assistant에 명령어가 온다면 라즈베리파이가 web request(REST API)를 받아서 그에 해당하는 동작을 실행 시킨다.

## REST API 란?
- `Resource` : URI
- `HTTP METHOD` : GET, REQUEST, PUT, DELETE
- `Representation of Resource` : JSON, XML, TEXT, RSS

URI에 GET, POST와 같은 방식을 사용해서 요청을 보내고 요청을 처리하기 위한 자원은 JSON, XML과 같은 형태로 주고 받는다.

---

# 간단한 시나리오

```
Go forward : 앞으로 가
Go back    : 뒤로 가
Go right   : 오른쪽으로 가
Go left    : 왼쪽으로 가
```

---

# 참조
- [https://www.instructables.com/id/Wi-Fi-Voice-Controlled-Robot-Using-Wemos-D1-ESP826/](https://www.instructables.com/id/Wi-Fi-Voice-Controlled-Robot-Using-Wemos-D1-ESP826/)
- [https://m.blog.naver.com/PostView.nhn?blogId=cosmosjs&logNo=221110517520&proxyReferer=https%3A%2F%2Fwww.google.com%2F](https://m.blog.naver.com/PostView.nhn?blogId=cosmosjs&logNo=221110517520&proxyReferer=https%3A%2F%2Fwww.google.com%2F)
