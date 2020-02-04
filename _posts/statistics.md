---
layout: post
title:  "딥러닝 공부할 때 알면 좋은 Statistics"
summary: "훑어보기 위해 적어본 확률론"
date:   2020-02-02 16:00 -0400
categories: math
---

네이버 부스트 캠프의 하버드 대학 확률론 기초 [강의 자료](https://www.edwith.org/harvardprobability)를 보며 정리했습니다.

- P(A) : P = 확률, A =  사건
- 표본공간 : 어떤 실험에서 가능한 모든 경우의 집합
- 사건 : 표본공간의 부분 집합

n명의 사람 중 k명을 선택하는 경우의 수

- 중복이 없고 순서를 고려하지 않을 때

  $$\binom{n}{k}$$

- 중복이 있고 순서를 고려할 때

  $$n^k$$

- 중복이 없고 순서를 고려할 떄

  $$n \times (n-1) \times \dots \times (n-k+1)$$

- 중복이 있고 순서를 고려하지 않을 때

  $$\binom{n+k-1}{k}$$
