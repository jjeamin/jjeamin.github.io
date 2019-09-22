---
layout: post
title:  "라즈베리파이 설정(라즈비안)"
summary: "자주 찾게되는 라즈베리파이 설정방법/명령어를 적어놓았습니다."
date:   2019-02-08 11:10 -0400
categories: pi
---

```
sudo apt-get update
sudo apt-get upgrade
```

## 랜선으로 연결하기
1. 네트워크 연결 탭 들어가기
2. 무선 네트워크 연결(속성) -> 공유탭 : 인터넷 연결 허용 체크
3. putty : `raspberrypi.mshome.net`

## SSH 설정

```
sudo raspi-config

Interfacing Options -> SSH

ifconfig : ip확인하기
```

## 한글설정

```
sudo raspi-config

Localisation Options -> Change locale,Change Keyboard Layout
```

1. local 설정하기
- en-US UTF 8
- en-GB UTF 8
- ko-KR UTF 8

2. 키보드 layout
- Other -> 한국 -> 104/105


3. 한글 깨짐 해결
```
sudo apt-get install ibus ibus-hangul fonts-unfonts-core
```

4. 한글 입력
```
ibus-setup -> english 제거 hangul 추가 -> reboot
```

---

## 화면 캡쳐

```
$ scrot
```

---

## 파이썬 패키지 경로

```
$ /usr/lib/python2.7/dist-packages
```

```
$ /usr/lib/python3.5/dist-packages
```

---

## 용량 확인

```
df -h
```

---

## 메모리 절약
1.스왑 사이즈 증가시키기

```
$ sudo vi /etc/dphys-swapfile
```

CONFIG_SWAPSIZE : 100 -> 1024

```
$ sudo /etc/init.d/dphys-swapfile stop
$ sudo /etc/init.d/dphys-swapfile start
```

2.부팅모드 변경하기

```
$ sudo raspi-config
```

- Boot Options => Desktop / CLI => Console Autologin
- Advanced Options => Memory Split : 16으로 설정

저장 후

```
$ reboot
```

3.메모리를 많이 사용하는 패키지는

```
pip --no-cache-dir install packages
```

---

## 부팅시 코드 자동실행

```
$ sudo vi /etc/profile/dbash_completion.sh
```

맨아래 실행파일 경로를 적어준다

```
/home/pi/경로/실행파일
```
