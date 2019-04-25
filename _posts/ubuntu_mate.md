---
layout: post
title:  "Ubuntu-mate 기본설정/사용하기"
summary: "Raspberry pi에서 Ubuntu mate를 사용해 보자"
date:   2019-04-25 12:00 -0400
categories: pi
---

# 구성
- raspberry 3 B+(ubuntu mate Bionic)

# 우분투 MATE 사용해보기

- 업데이트 해주세요

```
sudo apt-get update
sudo apt-get upgrade
```

## 해상도 설정
- 설정파일 들어가기

```
sudo vi /boot/config.txt
```

- 수정

```
hdmi_grop = 2

hdmi_mode = 보고 설정
```

- 리부팅

```
reboot
```

파일안에 기본적으로 모드별로 해상도가 적혀 있기 때문에 아주 간단하게 보면서 할 수 있다. 라즈비안에 비해서는 좋은거 같다.

## 한글설정

키보드가 한글입력을 하지않는다... 수정해보자!

- menu -> 제어센터 -> 언어지원

설치를 진행해봅시다.(조금 걸리네요..)

- 재부팅

```
reboot
```

- 우측 상단에 키보드 아이콘이 생성되었다. 왼쪽 마우스를 클릭한 뒤 `설정`을 누르고 `Hangul` 과 `키보드-영어(미국)`만 남긴 채로 `전역 설정`을 누르고 `입력기 전환`버튼을 누르고 `한/영`키를 누르면 `Hangul`로 전환 된다.

- 이제 한글설정 완료!!


+ 추후 하고싶은거 이것저것 진행 예정
