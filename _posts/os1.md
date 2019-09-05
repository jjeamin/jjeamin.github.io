---
layout: post
title:  "운영체제"
summary: "운영체제 이해하기"
date:   2019-09-02 13:00 -0400
categories: os
---

# 컴퓨터 시스템의 4가지 구성
- `하드웨어` : CPU, 메모리, I/O 장치
- `운영체제` : 컴퓨터를 시스템의 각종 하드웨어적인 자원과 소프트웨어적인 자원을 효율적으로 운영 관리함으로써 사용자가 시스템을 이용하는데 편리함을 제공하는 시스템 소프트웨어
- `응용 프로그램` : 사용자의 컴퓨팅 문제를 해결하기 위해 작성된 프로그램
- `사용자`

```
USER
 |
Application programs
 |
operating system
 |
computer hardware
```

# 운영체제
하드웨어를 효율적으로 사용하고 사용자 프로그램의 실행을 관리하는 자원 할당 및 제어 프로그램

`커널` : "컴퓨터에서 항상 실행되는 하나의 프로그램"

`미들웨어` : 데이터베이스, 멀티미디어, 그래픽과 같은 으용 프로그램 개발자에게 추가 서비스를 제공하는 소프트웨어 프레임워크

# Computer System Operation
- I/O 장치와 CPU가 동시에 실행될 수 있다.
- 각 장치 컨트롤러는 특정한 장치를 담당한다.
- 각 장치 컨트롤러는 local buffer가 있다.
- 각 장치 컨트롤러 유형에는 이를 관리하기 위한 운영체제 장치 드라이버가 있다.
- CPU는 데이터를 메인 메모리에서 local buffer로 또는 local buffer에서 메인 메모리로 옮긴다.
- 장치 컨트롤러는 CPU에 인터럽트를 발생시켜 작동의 완료를 알린다.

# 인터럽트의 기능
- 인터럽트는 일반적으로 모든 서비스 루틴의 주소를 포함하는 인터럽트 벡터를 통해 인터럽트 서비스 루틴으로 제어를 전송한다.

- 인터럽트 `archtecture`는 인터럽트된 명령어의 주소를 저장해야한다.

- 트랩 또는 예외는 오류나 사용자 요청으로 인해서 생기는 `software-generated interrupt`다.

- 운영체제가 인터럽트로 구동된다.

# 컴퓨터 시작
- 부팅시 부트스트랩 프로그램이 적재된다.

- 일반적으로 펌웨어라고 알려진 ROM,EPROM에 저장한다.

- 시스템의 모든 부분을 초기화

- 운영체제의 커널을 적재

- 시스템 데몬을 시작

- 커널 인터럽트 구동
  + 하드웨어

  + 소프트웨어
    + 오류
    + system call

# interrupt handling
- 운영체제는 레지스터와 프로그램 카운터(PC)를 저장하여 CPU상태를 유지한다.

- 인터럽트 유형 결정 : `polling`, `vectored interrupt system`

# I/O Structure
- `동기` : I/O가 시작된 후 I/O 완료시에만 제어가 사용자 프로그램으로 돌아간다.

  + 대기 명령은 다음 인터럽트까지 CPU를 유휴 상태로 만든다.

  + 동시 I/O처리는 없다.

- `비동기` : I/O가 시작된 후 I/O가 완료하지 않아도 사용자 프로그램으로 제어가 돌아간다.

  + `System call` : 사용자가 I/O 완료를 기다릴 수 있도록 운영체제에 요청
  + `Device-status table` : 유형, 주소 및 상태를 나타내는 각 I/O 장치에 대한 항목이 있다.

# Storage Structure
- `메인 메모리` : CPU가 직접 액세스 할 수 있는 대용량 저장 장치
  + 랜덤 액세스
  + 휘발성
  + DRAM


- `보조 저장장치` : 메인 메모리의 확장
  + 비휘발성


- `HDD` : 트랙 / 섹터


- `NVM 장치` : 비 휘발성 메모리 장치
  + 다양한 기술
  + 하드디스크 보다 빠르다
  + 비 휘발성

## Hirearchy

- `Speed` : 속도
- `Cost` :  비용
- `Volatility` : 휘발성

## Speed

```
 registers
    V
  cache
    V
main memory

nonvolatile memory
    V
hard-disk drives

optical dist
    V
magnetic tapes
```

`opposite` : `capacity`


# Unit
- `bit` -> `byte`(8 bits) -> `word`(8 bytes)
- `KB` : 2^10
- `MB` : 2^20
- `GB` : 2^30
- `TB` : 2^40
- `PB` : 2^50

# Multi Programming(Batch)
- 단일 유저는 CPU,I/O 장치를 항상 사용 중으로 유지할 수 없다.
- 전체 작업의 일부는 메모리에 보관
- 작업 스케쥴링에 의해 하나의 작업 선택,실행

# Multi Tasking(Timesharing)
- CPU가 작업을 자주 전환해서 실행중인 각 작업과 상호작용 할 수 있는 대화형 컴퓨팅
- 응답시간 1초 미만
- 메모리에서 프로세스를 실행하는 하나 이상의 프로그램을 가지고 있다.
- 작업 스케쥴링에 의해 예약
- Swapping

# Process
- 실행중인 프로그램, 시스템 내의 작업 단위
- 프로그램 카운터(PC) : 다음 명령어의 위치를 지정
- 다중 스레드 프로세스는 스레드 당 하나의 PC가 있다.
