---
layout: post
title:  "Google Assistant Robot"
summary: "Google Assistant Robot 만들기"
date:   2019-08-01 13:00 -0400
categories: pi
---

# Google Assistant Robot

Google Assistant를 이용해 명령으로 동작하는 Robot 만들기

# Dependency
- Google Assistant
- Raspberry Pi 3B+

---

# 간단한 시나리오

```
Go forward : 앞으로 가
Go back    : 뒤로 가
Go right   : 오른쪽으로 가
Go left    : 왼쪽으로 가
Hello      : 안녕(양손을 흔든다.)
bye        : 가지마(고개를 젓는다.)
see you    : 잘가(한손을 흔든다.)
```

---

# 중요한점

**Google Assistant의 기능을 살려야한다.!?**

# How????

방법1 : google assistant, webhook, IFTTT를 이용해 웹으로 통신

방법2 : 라즈베리파이를 구글어시스턴트로 사용하고 동작은 아두이노로.. esp8266 구매해야함

방법3 : google speech api 사용하기.. 요금이 발생

방법4 : 스마트미러 깔고 그 부분만 바꾼다.

방법5 : 앱인벤터 speech recognition

**최종 결정 방법** : 구글 어시스턴트의 기능을 살리면서 이용하려면 결국 구글 어시스턴트 샘플 소스코드에서 동작 추가하는 방법밖에 없는 것 같다.

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


# 방법2 : 아두이노

이것도 IFTTT 방식을 이용해서 사용하기 때문에 위에 것과 별다른 차이점이 없다.

```
google assistant ---(webhook)---> adafruit -------> IOT Device
```

문제점 : IoT Device의 ip를 신경쓸 필요가 없지만 esp8266를 구매해야한다.


# 방법3 : Google Speech API

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


# 방법4 : 스마트미러 수정하기

- [설치 강의](https://www.youtube.com/watch?v=O3l46ogmgLY)


# 방법5 : 앱인벤터로 제어하기

- 앱인벤터 : [http://appinventor.mit.edu/explore/](http://appinventor.mit.edu/explore/)
- 사용법 : [http://blog.naver.com/PostView.nhn?blogId=edisondl&logNo=221090848876](http://blog.naver.com/PostView.nhn?blogId=edisondl&logNo=221090848876)

---

# 최종 결정 방법 : 구글 어시스턴트 샘플 소스코드에 추가하기

google assistant sdk의 sample code인 `pushtotalk.py`를 수정해서 google assistant의 기능을 살리고 speech to text를 동작시키는 구문만 뽑아 오기로 했다.

## Robot 원리[예상]

`Snow boy` -> `google assistant` -> `action`,`tts`

---

# 1. Snow boy 설치

Snow boy는 wake up 단어를 설정해 이용할 수 있는 오픈소스 라이브러리다.

## 오디오 설치

```
$ sudo apt-get install python-pyaudio python3-pyaudio sox
$ sudo apt-get install portaudio19-dev
$ sudo apt-get install python-dev
$ pip install pyaudio
```

## swig 설치

```
$ sudo apt-get install swig
```

## git 설치

```
git clone https://github.com/kitt-ai/snowboy

$ sudo apt-get install libatlas-base-dev

$ cd snowboy
$ cd swig
$ cd Python3
$ make
```

## ERROR

- jack control error

```
jack_control start
```

- alsa error

아직 잡질 못했다....

```
Expression 'alsa snd_pcm_hw_params_set_period_size_near' ...
```

---

# 2. Google Assistant 설치

**참조** : [Here](https://jjeamin.github.io/pi/2019/07/09/googleapi/)

위에 google api를 설치 후에 sdk를 설치해서 예제 코드를 수정해 실행시키자

## sdk sample 설치

```
git clone https://github.com/jjeamin/assistant-sdk-python
```

## sdk sample 수정

```
vi assistant-sdk-python/google-assistant-sdk/googlesamples/assistant/grpc/pushtotalk.py
```

---

# 3. TTS 사용하기

동작 부분은 응답 요청을 없앨 예정이기 때문에 응답을 만들어 줘야할것 같다.

```
pip install gTTS
```

- [메뉴얼](https://gtts.readthedocs.io/en/latest/?ababcaca)

## 영어 쓰기

```python
from gtts import gTTS
tts = gTTS('hello', lang='en')
tts.save('hello.mp3')
```

## 한글 쓰기
```python
from gtts import gTTS
tts = gTTS('안녕', lang='ko')
tts.save('hello.mp3')
```

## 한글 영어 섞어 쓰기

```python
from gtts import gTTS

tts_en = gTTS(text='hello', lang='en')
tts_kr = gTTS(text='안녕하세요',lang='ko')

with open(FileName,'wb') as f            
  tts_en.write_to_fp(f)    # 영어로 한번 말하고
  tts_kr.write_to_fp(f)    # 한글로 한번 말하기
f.close()
```

---

## + 블루투스를 이용하기

시리얼 통신을 이용해서 블루투스를 이용하기 위해서는 기존의 블루투스의 기능을 없애줘야 하기 때문에 기능을 없애고 시작을 하기로 하자

```
sudo raspi-config
```

`Interfacing Options` -> `Serial Port Enable`, `Serial Console Disable`

```
sudo vi /boot/config.txt
```

맨아래로 가서 아래 코드 삽입

```
enable_uart=1
#disable bluetooth
dtoverlay=pi3-disable-bt
```

저장한 뒤에 아래 명령어를 사용하고 재부팅

```
sudo systemctl disable hciuart
```

python 코드 사용법

```python
import serial

ser = serial.Serial("/dev/ttyAMA0", "9600")
```

통신속도 확인

```
sudo stty -F /dev/ttyAMA0
```

---

## + 라즈베리파이 동영상 스트리밍

```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install git cmake libjpeg-dev imagemagick -y
```

```
cd mjpg-streamer/mjpg-streamer-experimental/
make CMAKE_BUILD_TYPE=Debug
sudo make install
```

```
./start.sh
```

```
127.0.0.1:8080 접속
```

---

## 결과 : [GitHub](https://github.com/jjeamin/Raspi_google_robot)

최종적으로 snowboy는 사용을 못했고 `gtts`와 `google assistant`를 이용해서 시나리오 형식으로 작성하였다.

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

- snowboy : [https://blog.naver.com/chandong83/221130096432](https://blog.naver.com/chandong83/221130096432)

- mjpg : [http://www.rasplay.org/?p=7174](http://www.rasplay.org/?p=7174)
