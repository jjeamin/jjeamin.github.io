---
layout: post
title:  "Google Assistant API, Giga Genie API"
summary: "라즈베리파이에서 google API, giga genie API(AI Maker Kit) 실행하기"
date:   2019-07-09 13:00 -0400
categories: pi
---

# Google API

Google Assistant API를 사용하는 방법에 대해서 리뷰 할 것이다.

# Google cloud
- [https://console.cloud.google.com/home/dashboard?project=frontu](https://console.cloud.google.com/home/dashboard?project=frontu) 접속

1. 프로젝트를 생성한다.
2. API 및 서비스의 대시보드 탭에 들어간다.
3. API 및 서비스 사용설정을 클릭한다.
4. Google Assistant API를 검색하고 활성화를 누른다.

# OAuth Client ID 생성
1. API 및 서비스 -> 사용자 인증 정보 -> 사용자 인증 정보 만들 -> OAuth 클라이언트 ID

2. 기타 선택 후 생성

3. OAuth 2.0 클라이언트 ID 탭에서 기타 클라이언트의 맨 오른쪽 아래 화살표를 클릭해 JSON 파일을 다운로드 받는다.

4. 다운받은 JSON 파일을 라즈베리파이에 옮겨 넣는다.

# Raspberry pi

라즈베리파이에 접속해서 작업을 시작하는 부분이다.

# 스피커 설정

```
// 마이크
$ arecord -l
// 스피커
$ aplay -l
```

```
vi .asoundrc
```

```vim
pcm.!default {
  type asym
  capture.pcm "mic"
  playback.pcm "speaker"
}

pcm.mic {
  type plug
  slave {
    pcm "hw:<card number>,<device number>"
  }
}

pcm.speaker {
  type plug
  slave {
    pcm "hw:<card number>,<device number>"
  }
}
```

## 마이크/스피커 테스트

- 마이크

```
// 녹음
arecord --format=S16_LE --duration=5 --rate=16000 --file-type=raw out.raw
// 듣기
aplay --format=S16_LE --rate=16000 out.raw
```

- 스피커

```
// 왼쪽 오른쪽
speaker-test -t wav
```

## 사운드 조절

```
alsamixer
```

## 라이브러리 설치

```
$ sudo apt-get update
$ sudo apt-get install portaudio19-dev libffi-dev libssl-dev
```


## 가상환경 설정

```
$ sudo apt-get install python3-dev python3-venv
$ python3 -m venv py3
$ py3/bin/python -m pip install --upgrade pip setuptools
$ source py3/bin/activate
```

## Google Assistant 설치

```
$ python -m pip install --upgrade google-assistant-library
$ python -m pip install --upgrade google-assistant-sdk[samples]==1.0.0
$ python -m pip install --upgrade google-auth-oauthlib[tool]==0.5.1
```

## Credential 생성

```
$ google-oauthlib-tool --scope https://www.googleapis.com/auth/assistant-sdk-prototype --save --headless --client-secrets /path/to/client/json
```

1. url 접속
2. 계정 로그인
3. 동의 클릭
4. 생성된 코드를 터미널에 적는다.

## 디바이스 등록

```
googlesamples-assistant-devicetool register-model --manufacturer "NAME/manufacturer" --product-name "NAME/PRODUCT" --description "INSERT/descript" --type LIGHT --model "INSERT/MODEL"
```


## 실행

```
googlesamples-assistant-hotword --device_model_id my-model
```


# 참조
- [https://ukayzm.github.io/installing-google-assistant/](https://ukayzm.github.io/installing-google-assistant/)
- [https://diy-project.tistory.com/88](https://diy-project.tistory.com/88)
- [https://diy-project.tistory.com/89](https://diy-project.tistory.com/89)
- [https://diy-project.tistory.com/91](https://diy-project.tistory.com/91)
