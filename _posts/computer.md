---
layout: post
title:  "Computer Log"
summary: "내가 찾아본 Computer 관련 기록"
date:   2020-01-28 09:10 -0400
categories: computer
---

[이 곳](https://ko.khanacademy.org/computing/computer-science/how-computers-work2)을 보면서 정리한 자료입니다.

# 컴퓨터의 구성
컴퓨터는 어떻게 동작하나?

## Computer
전기신호를 on/off하는 것으로 통제되는 기계

## HOW??

```
Input -> Storage(Memory) -> Processing(CPU) -> Output
```

- `Input` : 키보드, 마우스, 카메라, 등등
- `Storage`, `Processing` : 데이터는 메모리에 저장된 후 프로세서에서 정보를 전달 그리고 정보를 처리한 뒤 출력한다.
- `Output` : 화면, 사진, 글씨 등등

## Data
- 0, 1
- on, off
- yes, no

#### Decimal
- 0 ~ 10로 수를 표현
- 10진수

#### Binary
- 0, 1로 수를 표현
- 2진수
- 모든 Text, Image, Sound

## 회로
- 입력을 출력으로 보내려면 컴퓨터가 처리를 해야한다. 그렇기 때문에 컴퓨터는 0과 1을 다룰 수 있는 회로로 이루어져 있다.
- AND, OR, XOR, NOT 와 같은 단순한 연산을 가진 회로를 조합해서 처리를 한다.
- 작으면 이동 거리가 작기 때문에 빠르다.


키보드를 A 하나 누르면 글자 하나를 숫자로 변경하고 CPU는 A를 어떻게 만들지에 대한 명령을 Memory에게 요청한다. 그리고 A를 만들고 결과를 Memory에 저장한 뒤 스크린으로 A의 픽셀 정보를 전송한다.

---
# 컴퓨터의 통신

서로 다른 컴퓨터는 어떻게 데이터를 주고 받나??

## OSI 7계층
- Open System Interconnection
- 컴퓨터는 어떻게 통신하는지에 대한 답

간단히 표현하면 아래와 같다.

```
사용자 < - > 사용자
```

하지만 간단히 표현 하지 않고 본다면 아래와 같다.

```
   사용자                        사용자
     |                             |
 Application                   Application
     |                             |
Presentation                  Presentation
     |                             |
   Session                      Session
     |                             |
  Transport                     Transport
     |                             |
  Network                       Network
     |                             |
 Data Link                     Data Link
     |                             |
  Physical        <==>          Physical
```

계층을 넘어갈 때 마다 사용자가 보내는 데이터에 추가적인 데이터를 붙여서 전송을 한다. 그리고 Physical에서 넘어갈 때 그 추가적인 데이터를 하나하나씩 떼어서 다른 사용자에게 전송되는 과정이다.

계층에 대해서 알아보자!

## Physical
- 물리적 계층
- 전기신호를 보낸다.

## Data Link
- 데이터 링크 계층
- 물리적주소(맥주소)를 가지고 통신한다.

## Network
- 네트워크 계층
- 보낼 경로, 주소를 설정한다.
- 패킷을 전송한다.
- IP

## Transport
- 전송 계층
- 전송에 실패한 것을 다시 보내거나, 유효하지 않은지 확인을 해준다.
- 신뢰성이 있는 통신을 보장한다.
- TCP

## Session
- 세션 계층
- 여러 컴퓨터와 대화를 하기 위해서 필요한 통로를 만들고 없애는 부분
- TCP / IP 세션을 생성,제거하는 부분
- 포트를 연결하는 곳

## Presentation
- 표현 계층
- 대화 내용을 암호화, 복호화를 해준다.
- 대화 내용이 어떤 데이터인지 표현을 해준다.(text, img, 등등)

## Application
- 응용 계층
- 여러가지 응용 프로그램
- 여러 컴퓨터와 대화를 하기위해 눈으로 볼 수 있는 웹 브라우저를 예로 들 수 있다. 크롬, 사파리 등등
- 프로토콜(약속) : HTTP, FTP, Telnet 등등

한줄 요약 : 대화 내용을 작성 -> 어떤 내용이 들어있는지 표현 -> 다른 컴퓨터와 대화하기 위한 통로 생성 -> 패킷을 생성 -> 보낼 경로를 찾음 -> 패킷을 전송 -> 전기신호
