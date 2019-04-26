---
layout: post
title:  "SORT(SIMPLE ONLINE AND REALTIME TRACKING)"
summary: "SORT 논문 읽어보기"
date:   2019-04-25 12:00 -0400
categories: paper
---

# SORT
- 실시간 객체 추적을 해줄수 있는 algorithm 입니다.
- [GITHUB](https://github.com/abewley/sort)
- [PAPER](https://arxiv.org/abs/1602.00763)

*논문을 읽어보고 코드 분석을 하겠습니다.*

# ABSTRACT
`SORT`는 실시간 추적을 위해 object들을 효과적으로 연관지어주는 것이 주 목적인 multi object tracking에 대한 실용적인 방법을 탐구하는 논문이다. detector 부분을 변경하는 것만으로도 tracking을 최대 18.9%까지 향상시킬 수 있다. `Kalman Filter`와 `Hungarian algorithm`과 같은 익숙한 기술을 기초로 하는 것만으로도 최신 online tracker에 필적하는 정확성을 제공한다. 또한 tracking하는 방식이 단순하기에 그에 따른 속도향상도 매우 높다.

- kalman filter 정리 -> [HERE](https://jjeamin.github.io/kalman/2019/04/23/kalman/)

# INTRODUCTION
이 논문은 object가 각 frame에서 detection되고 bounding box로 표현되는 multiple object tracking(MOT) 문제에 대한 추적-탐지 프레임워크의 약식 구현을 제시한다.(YOLO에 적용하기 좋을 것 같다..). 다른 tracking 접근법과 달리 online tracking을 대상으로 한다. 그리고 실시간 추적을 효과적으로 하고 보행자 추적과 같은 애플리케이션의 성능 증진을 위한 효율성에 초점을 둔다.

- MOT문제는 video seqence의 여러 frame에서 detection을 연관시키는 것이 목적인 data association 문제로 볼수 있다. data association을 돕기 위해 tracker는 sequence의 object 및 appearance를 모델링하는 다양한 방법을 사용한다.

- 이 논문에서 사용된 방법은 최근에 확립 된 시각적 MOT benchmark를 관찰하며 동기를 부여받았다.

- 첫째, MHT(Multiple Hypothesis Tracking)과 JPDA(Joint Probabilistic Data Association)를 포함해  

# 참조
- [https://github.com/abewley/sort](https://arxiv.org/abs/1602.00763)
- [https://arxiv.org/abs/1602.00763](https://arxiv.org/abs/1602.00763)
