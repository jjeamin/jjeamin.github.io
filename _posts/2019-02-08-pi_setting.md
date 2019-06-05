---
layout: post
title:  "자주 찾는 명령어(라즈비안)"
summary: "자주 찾게되는 명령어들을 적어놓았습니다."
date:   2019-02-08 11:10 -0400
categories: pi
---

## 한글설정

```
sudo raspi-config
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


## 화면 캡쳐

```
$ scrot
```


## 파이썬 패키지 경로

```
$ /usr/lib/python2.7/dist-packages
```

```
$ /usr/lib/python3.5/dist-packages
```

## 용량 확인

```
df -h
```

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

## 부팅시 코드 자동실행

```
$ sudo vi /etc/profile/dbash_completion.sh
```

맨아래 실행파일 경로를 적어준다

```
/home/pi/경로/실행파일
```
