---
layout: post
title:  "MobileNetv2"
summary: "MobileNetv2 논문 읽어보기"
date:   2019-11-21 13:00 -0400
categories: paper
use_math: true
---

# MobileNetv2

(MobileNetV2: Inverted Residuals and Linear Bottlenecks)

- MobileNetv2 Paper : [Here](https://arxiv.org/abs/1801.04381)
- Official Code : [Here](https://github.com/tensorflow/models/blob/master/research/slim/nets/mobilenet/mobilenet_v2.py)


# Introduce

- MobileNet의 2번째 버전을 제안한다.

- shortcut connection이 thin bottleneck layers 사이에 있는 `inverted residual structure`를 기반으로 한다.

- 중간에 확장하는 layer는 lightweight depthwise convolution을 사용해 비선형적으로 feature를 필터링한다. 그리고 표현력을 유지하기 위해 좁은 layers에서 비선형 성을 제거하는 것이 중요하다.

# Abstract
정확도를 유지하면서 필요한 작업의 수와 메모리를 대폭 줄인 모바일 맞춤형 모델이다. 주목해야 할 점은 새롭게 제안한 모듈 `inverted residual`이다. 이 모듈은 linear bottleneck을 가진다. 먼저 low-dimensional를 입력으로하고 high-dimension으로 확장된 후에 lightweight depthwise convolution을 지나서 필터링된다. 그리고 다시 linear convolution을 사용해서 low-dimension이 된다.

이 모듈을 사용해서 모바일에 적합한 Network가 된다.
