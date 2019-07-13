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
- IFTTT[미정]
- Google Assistant
- Raspberry Pi 3B+

---

# 간단한 시나리오

```
Go forward : 앞으로 가
Go back    : 뒤로 가
Go right   : 오른쪽으로 가
Go left    : 왼쪽으로 가
```

---

# How????

방법1 : google assistant, webhook, IFTTT를 이용해 웹으로 통신

방법2 : 라즈베리파이를 구글어시스턴트로 사용하고 동작은 아두이노로.. esp8266 구매해야함

방법3 : google speech api 사용하기

방법4 : 스마트미러 깔고 그 부분만 바꾼다.

방법5 : 앱인벤터 speech recognition

**최종 결정 방법** : 구글 어시스턴트 샘플 소스코드에 추가하기

---

# 방법 1 : IFTTT
- If This Then That : 서로를 연동시켜주는 서비스

```
라즈베리파이(GPIO)  <->  Google Assistant
Arduino            <->  Google Assistant
```

## IFTTT 설정하기
- This : `google assistant`
- That : `webhook`

google assistant에 명령어가 온다면 라즈베리파이가 web request(REST API)를 받아서 그에 해당하는 동작을 실행 시킨다.

## REST API 란?
- `Resource` : URI
- `HTTP METHOD` : GET, REQUEST, PUT, DELETE
- `Representation of Resource` : JSON, XML, TEXT, RSS

URI에 GET, POST와 같은 방식을 사용해서 요청을 보내고 요청을 처리하기 위한 자원은 JSON, XML과 같은 형태로 주고 받는다.

*IFTTT에서 어시스턴트의 음성인식을 Trigger로 사용하고 Webhooks 을 이용해서 라즈베리파이에 동작을 Reqeust할 것이다. 이게 제일 쉬워 보인다.
IFTTT에서 reqeust를 받기 위해서는 라즈베리파이에 Flask 서버를 돌려야 한다. 먼저 Flask를 설치하자.*

문제점 : google assistant 명령을 받아서 던져줄 서버가 필요하다....

```
google assistant ---(webhook)---> Flask --------> Raspberry Pi
```

---

# 방법2 : 아두이노 구현

이것도 IFTTT 방식을 이용해서 사용하기 때문에 위에 것과 별다른 차이점이 없다.

```
google assistant ---(webhook)---> adafruit -------> IOT Device
```

문제점 : IoT Device의 ip를 신경쓸 필요가 없지만 esp8266를 구매해야한다.

---

# 방법3 : Google Speech API 사용하기

이 방법이 동작하는게 제일 간편하다. 하지만 google assistant의 기능을 넣을 수 있는지 아직 잘 모르겠다.

## 필요 모듈 설치하기

- google speech library

```
pip install SpeechRecognition
```

- pyaudio library

```
sudo apt-get install python-pyaudio python3-pyaudio

pip install pyaudio
```

- [Sample Code](https://webnautes.tistory.com/1247)

---

# 방법4 : 스마트미러 다운받고 부분수정

- [설치 강의](https://www.youtube.com/watch?v=O3l46ogmgLY)

---

# 방법5 : 앱인벤터를 이용해 제어하기

- 앱인벤터 : [http://appinventor.mit.edu/explore/](http://appinventor.mit.edu/explore/)
- 사용법 : [http://blog.naver.com/PostView.nhn?blogId=edisondl&logNo=221090848876](http://blog.naver.com/PostView.nhn?blogId=edisondl&logNo=221090848876)

---

# 최종 결정 방법 : 구글 어시스턴트 샘플 소스코드에 추가하기

google assistant sdk의 sample code인 `push to talk.py`를 수정해서 speech to text를 동작시키는 구문만 뽑아 오기로 했다.



---

# 부록
- `gtts` : 음성합성

- `aiy 소스코드` : [https://m.blog.naver.com/roboholic84/221251421903](https://m.blog.naver.com/roboholic84/221251421903)

- `aiy 예제 소스코드` : [https://m.blog.naver.com/roboholic84/221251421903](https://m.blog.naver.com/roboholic84/221251421903)

- `aiy 예제 깃허브` : [https://github.com/google/aiyprojects-raspbian](https://github.com/google/aiyprojects-raspbian)

- `wake up 변경` : [https://steemit.com/utopian-io/@neavvy/google-assistant-on-raspberry-or-part-3-custom-wake-word](https://steemit.com/utopian-io/@neavvy/google-assistant-on-raspberry-or-part-3-custom-wake-word)

# 참조
- [https://www.instructables.com/id/Wi-Fi-Voice-Controlled-Robot-Using-Wemos-D1-ESP826/](https://www.instructables.com/id/Wi-Fi-Voice-Controlled-Robot-Using-Wemos-D1-ESP826/)
- [https://m.blog.naver.com PostView.nhn?blogId=cosmosjs&logNo=221110517520&proxyReferer=https%3A%2F%2Fwww.google.com%2F](https://m.blog.naver.com/PostView.nhn?blogId=cosmosjs&logNo=221110517520&proxyReferer=https%3A%2F%2Fwww.google.com%2F)
- [https://webnautes.tistory.com/1247](https://webnautes.tistory.com/1247)


- Adafruit : [https://passionbull.net/2018/12/others/iot-switch-adafruit-ifttt-google-assistant/](https://passionbull.net/2018/12/others/iot-switch-adafruit-ifttt-google-assistant/) - esp8266
