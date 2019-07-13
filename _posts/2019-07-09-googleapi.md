---
layout: post
title:  "Google Assistant API"
summary: "라즈베리파이에서 google API 실행하기"
date:   2019-07-09 13:00 -0400
categories: pi
---

# API vs SDK
- `API` : Application Programming Interface
- `SDK` : Software Development Kit

## API
응용 프로그램 간에 연동을 위해 사용되는 개념으로 기능을 공유 할 수 있다.

## SDK
소프트웨어 개발 도구 모음, 거의 API와 같은 의미로 사용된다.

---

# Google API

Google Assistant API를 사용하는 방법에 대해서 리뷰 할 것이다.

# Google action console
- [https://console.actions.google.com/](https://console.actions.google.com/)

1. 프로젝트를 생성한다.
2. 아래 `Device registration`을 실행한다.



![device](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/google/device.PNG)



# Google cloud
- [https://console.cloud.google.com/home/dashboard](https://console.cloud.google.com/home/dashboard) 접속

1. API 및 서비스 -> 대시보드
2. API 및 서비스 사용설정 클릭
3. Google Assistant API를 검색하고 활성화를 누른다.

# OAuth Client ID 생성
1. API 및 서비스 -> 사용자 인증 정보 -> 사용자 인증 정보 만들기 -> OAuth 클라이언트 ID

2. 기타 선택 후 생성

3. OAuth 2.0 클라이언트 ID 탭에서 기타 클라이언트의 맨 오른쪽 아래 화살표를 클릭해 JSON 파일을 다운로드 받는다.

4. 다운받은 JSON 파일을 라즈베리파이에 옮겨 넣는다. `(경로 : /home/pi/)`

---

# Raspberry pi

라즈베리파이에 접속해서 작업을 시작하는 부분이다.

## 스피커 설정

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

---

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
```

```
$ python -m pip install --upgrade google-assistant-sdk[samples]
$ python -m pip install --upgrade google-auth-oauthlib[tool]
```

## Credential 생성

```
$ google-oauthlib-tool --scope https://www.googleapis.com/auth/assistant-sdk-prototype --save --headless --client-secrets /path/to/client/json
```

1. url 접속
2. 계정 로그인
3. 동의 클릭
4. 생성된 코드를 터미널에 적는다.

---

# 실행

- Library

```
$ googlesamples-assistant-hotword --device_model_id my-model
```

- Service

```
$ googlesamples-assistant-pushtotalk --project-id "INPUT/project-id" --device_model_id "INPUT/device-model-id"
```

---

이상하게 pushtotalk이 되면 hotword가 안되고 그런다.. 구글 메뉴얼도 pushtotalk을 이용한 service 예제만 활성화 시켰기 때문에 나중에 기회가 되면 찾아봐야겠다. 일단 필요한건 pushtotalk이기 때문에 넘어가도록 하자

- `Service` : pushtotalk 사용가능
- `Library` : hotword 사용가능

---

# 부록 : 한국어로 대화하기

```
$ googlesamples-assistant-pushtotalk  --lang ko-KR
```

# 부록 : 디바이스 등록[명령어]

*위에 디바이스 등록을 했으면 안해도 된다.*

```
$ googlesamples-assistant-devicetool register-model --manufacturer "INPUT/manufacturer" --product-name "INPUT/product" --description "INPUT/descript" --type LIGHT --model "INPUT/model"
```

---

# 참조
- [https://ukayzm.github.io/installing-google-assistant/](https://ukayzm.github.io/installing-google-assistant/)
- [https://diy-project.tistory.com/88](https://diy-project.tistory.com/88)
- [https://diy-project.tistory.com/89](https://diy-project.tistory.com/89)
- [https://diy-project.tistory.com/91](https://diy-project.tistory.com/91)
- [https://www.sigmdel.ca/michel/ha/rpi/voice_rec_02_en.html#decoder](https://www.sigmdel.ca/michel/ha/rpi/voice_rec_02_en.html#decoder)
