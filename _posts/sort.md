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
이 논문은 object가 각 frame에서 detection되고 bounding box로 표현되는 multiple object tracking(MOT) 문제에 대한 추적-탐지 프레임워크의 약식 구현을 제시한다.(YOLO에 적용하기 좋을 것 같다..). 다른 tracking 접근법과 달리 online tracking을 대상으로 한다. 그리고 실시간 추적을 효과적으로 하고 보행자 추적과 같은 애플리케이션의 성능 증진을 위한 효율성에 초점을 둔다. MOT문제는 video seqence의 여러 frame에서 detection을 연관시키는 것이 목적인 data association 문제로 볼수 있다. data association을 돕기 위해 tracker는 sequence의 object 및 appearance를 모델링하는 다양한 방법을 사용한다.

**이 논문에서 사용된 방법은 최근에 확립 된 시각적 MOT benchmark를 관찰하며 동기를 부여받았다.**

- 첫째, MHT(Multiple Hypothesis Tracking)과 JPDA(Joint Probabilistic Data Association)를 포함해 benchmark의 상위 순위를 차지하는 훌륭한 data association를 다시 등장시켰다.

- 둘째, ACF(Aggregate Channel Filter)를 사용하지 않은 tracker는 최상위 tracker이기 때문에 detection의 quality가 다른 detector를 방해할 수도 있다는 것을 나타낸다. 또한 accuracy와 speed 사이의 trade-off가 명확하게 나타난다.

- `Occam's Razor` 에 일치하도록, detection 구성요소를 벗어난 appearance은 tracking에서 무시되고, motion estimation과 data association에 모두 bounding box의 위치와 크기만 사용된다.

- 단기적, 장기적 `occlusion`에 관한 문제가 매우 드물게 발생하고 명시적인 치료로 인해 tracking framework에 바람직하지 않은 복잡성이 도입되므로 무시된다. object의 재증명 형태로 복잡성을 추가하면 tracking framework에 상당한 overhead가 추가되어서 실시간 detector에서의 사용이 제한될 수 있다고 주장한다.

- 이 설계는 다양한 edge cases 와 detection error를 처리하기 위해 다양한 구성요소를 통합 시킨 visual tracker들 과 대조적이다. 대신 공용 frame-to-frame 연관성에 효율적이고 믿을수 있는 처리에 초점을 맞춘다.

- detection errors에 대한 견고성을 목표로하기 보다는 visual object detection의 최근 발전을 이용하여 detection 문제를 직접 해결한다. 이는 보편적인 ACF 보행자 검출기와 CNN기반 검출기를 비교함으로써 입증된다.

- 추가적으로, 고전적이지만 매우 효율적인 두가지 방법인 `Kalman ﬁlter`,`Hungarian method`이 각각 tracking 문제의 움직임 예측과 데이터 연관요소를 처리하는데 사용된다. 이 논문에서의 접근법은 다양한 환경에서 human tracking에서만 사용하지만 다른 object에서도 가능하다.


# LITERATURE REVIEW
전통적으로 MOT와 JPA를 사용해 해결했지만 불확실성이 높은 상태에서 어려운 결정을 지연시킨다.  그래서 객체 수가 기하급수적인 동적인 환경에서는 실시간 응용 프로그램에 비실용적이다.

- 최근에 Rezatoﬁghi et al는 `integer programs`의 문제를 해결할 때 최근 발전을 이용해 JPDA의 효율적인 근사치를 통해 `combinatorial complexity` 문제를 해결하기 위한 목적으로 MOT에서 JPDA공식을 재 검토했다.

- 유사하게 Kim et al은 최첨단 성능을 달성하기 위해 각 타겟에 대한 `appearance models`을 사용해 MHT그래프를 잘랐다. 그러나 이러한 방법으로는 의사결정이 지연되기 때문에 online tracking에 적합하지 않다.

- Geiger et al에 의한 방법은 2단계 처리에 `Hungarian algorithm`을 사용한다. 첫번째로 인접한 frame에 detection을 연결해서 기하학과 모양새가 결합되어 `tracklets`을 형성되고




# 용어
- `Occam's Razor` : 절감의 법칙(어떤 현상을 설명하는 데는 가장 단순한 가설로 시작해야하고 가설을 필요 이상으로 정립하지 말라는 것)

- `occlusion` : object를 추적하는 시스템을 개발하는 경우, 추적중인 object가 다른 object에 숨겨진 경우를 뜻한다.

### Hungarian algorithm
할당문제(assignment problem)를 해결하기 위한 알고리즘이고 할당문제란 n명의 사람에게 중복없이 일을 부여하고 총 할당 비용을 최소로 하는 해를 구하는 것이라고 정의할 수 있다.


1. 각 할당되는데 발생하는 비용을 비용행렬로 만들어서 각 행과 열에서 최소값을 제거한다.

2. 최소의 라인으로 0의 성분을 가진 행과 열을 지운다.

3. 지워지지 않는 성분 중 최소값을 찾아 이를 지워지지 않는 성분에서 빼고 각 라인이 겹치는 성분에서는 더한다.

4. 각 열과 행에 0이 하나씩 있도록 0의 성분을 찾는다.

# 참조
- [https://github.com/abewley/sort](https://arxiv.org/abs/1602.00763)
- [https://arxiv.org/abs/1602.00763](https://arxiv.org/abs/1602.00763)
