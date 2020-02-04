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

## Reference
- [https://www.html5rocks.com/en/tutorials/internals/howbrowserswork/#Introduction](https://www.html5rocks.com/en/tutorials/internals/howbrowserswork/#Introduction)
- [https://www.edwith.org/boostcourse-web/joinLectures/12943](https://www.edwith.org/boostcourse-web/joinLectures/12943)
