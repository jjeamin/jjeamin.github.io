---
layout: post
title:  "git Log"
summary: "git 자주 쓰는 명령어 모으기"
date:   2019-12-31 09:10 -0400
categories: git
---

# Git

깃허브를 사용할 때 commit, push, pull만 사용하는거 같아서 뭔가 쓸데없는 작업도 많고 너무 한정적이게 사용하는 것 같아서 제대로 사용하기 위해서 다시는 실수하지 않기 위한 기록을 남긴다.

# Git project page 만들기

- 참조 : [http://dogfeet.github.io/articles/2012/github-pages.html](http://dogfeet.github.io/articles/2012/github-pages.html)

`orphan` : 부모가 없는 branch를 생성하는 명령어

```
$ git checkout --orphan gh-pages

$ git rm -rf .
```

```
$ echo "My GitHub Page" > index.html
$ git add .
$ git commit -a -m "First pages commit"
$ git push origin gh-pages
```

원격 저장소에서 settings -> GitHub Pages -> Source -> gh-pages branch 선택

그 이후에 gh-pages branch에서 페이지 작업을 진행하면 된다. 

- 코드 따로
- 페이지 따로

# reset 한거 되돌리기

```
$ git reflog
```
목록을 보고 찾은 어디로 이동할지 찾은 뒤에

```
$ git reset --hard HEAD@{23}
```

원격 저장소까지 변경시키려면 --force를 추가한다.

```
$ git push --force
```