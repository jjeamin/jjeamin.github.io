---
layout: post
title:  "WEB"
summary: "내가 찾아본 Web 관련 기록"
date:   2020-01-11 16:00 -0400
categories: web
---

## HTTP
HTTP : Hypertext Transfer Protocol

port number : 80

```
client -> (request) -> server
server -> (response) -> client
```

```
GET : 정보를 요청하기 위해서 사용한다. (SELECT)
POST : 정보를 밀어넣기 위해서 사용한다. (INSERT)
PUT : 정보를 업데이트하기 위해서 사용한다. (UPDATE)
DELETE : 정보를 삭제하기 위해서 사용한다. (DELETE)
HEAD : (HTTP)헤더 정보만 요청한다. 해당 자원이 존재하는지 혹은 서버에 문제가 없는지를 확인하기 위해서 사용한다.
OPTIONS : 웹서버가 지원하는 메서드의 종류를 요청한다.
TRACE : 클라이언트의 요청을 그대로 반환한다. 예컨데 echo 서비스로 서버 상태를 확인하기 위한 목적으로 주로 사용한다.
```

## Cookie
기록을 추적하는 기술

페이스북이 이것을 이용해 사람들의 관심사를 추출한다고 말이 많았다.


## 브라우저 처리 과정

```
HTML -> HTML Parser -> DOM Tree       layout
                          |             |
                      attachment -> Render Tree -> Painting -> Display
                          |
CSS  -> CSS parser  -> DOM Tree
```

거의 모든 자료의 구조가 트리로 되어있다.

## Dom이란
Document Object Model의 약자다.
자바스크립트는 동적으로 속성,요소,스타일,이벤트를 추가,제거,수정 할수 있다. 자바스크립트를 사용할 때 DOM API를 사용한다. DOM은 실질적으로 일이 일어나고 있는 곳이다.

Dom은 문서를 조작하고 접근하는 하나의 방법을 이야기한다고 할수 있다.

# React
웹사이트를 만들기 위해서 동적으로 화면을 보여주는 웹을 만들기 위해서는 자바스크립트를 사용해야한다. 그때 일일히 DOM에 접근해서 원하는 작업을 해주어야하는데 관리가 어렵다. 그래서 자바스크립트 라이브러리를 사용해서 개발하는 것이 좋다. React는 자바스크립트 프레임 워크로 웹, 앱 개발이 가능하다. 그래서 이번에 앱개발을 하는 김에 React로 웹까지 잡아버려야겠다.

[사용법](https://velopert.com/3621)을 많이 참조하였고 이 글에는 궁금한 정의같은 것을 위주로 적을 것이다.

## Expo
핸드폰에서 개발하기 쉽게 Expo를 사용한다. 지원이 잘돼서 매우 좋다!

*안드로이드 기준*

- node js를 설치한다.
- [Expo](expo.io)에서 회원가입한다.
- expo cli를 설치한다.

```
npm install expo-cli --global
```

- expo 프로젝트를 생성한다.

```
expo init your_proj_name
cd your_proj_name
expo start
```

- expo 어플리케이션을 설치한다.
- 어플리케이션 설치 후 QR코드를 인식해서 연동시킨다.(Tunnel로 변경)








## Reference
- [https://www.html5rocks.com/en/tutorials/internals/howbrowserswork/#Introduction](https://www.html5rocks.com/en/tutorials/internals/howbrowserswork/#Introduction)
- [https://www.edwith.org/boostcourse-web/joinLectures/12943](https://www.edwith.org/boostcourse-web/joinLectures/12943)
- [https://velopert.com/3621](https://velopert.com/3621)
