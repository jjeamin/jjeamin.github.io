---
layout: post
title:  "git Log"
summary: "git 자주 쓰는 명령어 모으기"
date:   2019-12-31 09:10 -0400
categories: git
---

# Git

깃허브를 사용할 때 commit, push, pull만 사용하는거 같아서 뭔가 쓸데없는 작업도 많고 너무 한정적이게 사용하는 것 같아서 제대로 사용하기 위해서 다시는 실수하지 않기 위한 기록을 남긴다.

# reset 한거 되돌리기

```
$ git reflog

목록을 보고 찾은 어디로 이동할지 찾은 뒤에

$ git reset --hard HEAD@{23}

되돌리기
```
