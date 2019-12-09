---
layout: post
title:  "grad CAM"
summary: "grad CAM 논문 읽어보기"
date:   2019-12-03 13:00 -0400
categories: paper
use_math: true
---

# grad CAM

(Grad-CAM: Visual Explanations from Deep Networks via Gradient-based Localization)

- grad CAM paper : [Here](https://arxiv.org/abs/1610.02391)
- Official Code : [Here](https://github.com/ramprs/grad-cam)



![figure1](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/cam/grad_figure1.PNG)



# Abstract

CAM(class activation mapping)과 유사하게 CNN 모델로부터 input data의 '중요한(important)'영역을 시각화 시켜주는 기술이다.

Grad-CAM은 CNN의 각 class마다 마지막 convolutional layer로 가는 gradient 정보를 사용해 이미지에서 중요한 영역의 대략적인 localization map을 만들어낸다.

즉, CAM이나 Grad-CAM으로 모델을 학습시키는 사람들이 어떠한 class를 잘 예측하는지에 대한 신뢰도를 향상시킨다.(약한 모델, 강한 모델)

# Introduction

누구나 모델을 학습시킬때 결과물이 좋지 않다면 왜 좋지 않은지 궁금하다.(나 또한..) 그래서 딥러닝을 의미있게 학습시키기 위해서는 어떻게 모델이 data를 예측하는지에 대해서 설명할 수 있는 명확한 모델을 만들어야한다. 그래서 이 논문에서는 Grad-CAM을 제안하였고 위에 그림을 보면 원하는 class의 object가 어디에 존재하는지 시각화를 해줄수 있다.

1. 이 논문에서는 기존의 모델의 구조를 변경하지 않고 시각적인 설명을 생성하는데 사용할 수 있는 Grad-CAM을 제안한다.(**CAM과의 차이점**)
3. 모델의 신뢰성을 높일 수 있고 약한 모델과 강한 모델을 구분지을수 있게 돕는다.

기존의 CAM은 fully conntected layer를 convolutional layer, global average pooling으로 대체하면서 CNN 모델의 구조를 변경시킨다.

```
Feature Maps(final convolutional layer output)
                    |
                (weights)
                    |
          Global Average Pooling
                    |
                (weights)
                    |
                 Softmax
```

GAP(global average pooling)이 없다면 weights로 CAM을 구하는 작업을 할 수 없기 때문에 마지막 layer에 GAP이 무조건 있어야한다. 이러한 구조는 일반 모델과 비교해서 정확도가 낮아지거나 다른 작업(image captioning, visual question answering 등..)에는 적용할 수 없다(Softmax가 없는 경우).

Grad-CAM은 이러한 단점을 보완시키기 위해서 **gradient를 이용해서 feature map을 결합** 하는 새로운 방법이다.

### image captioning

이미지가 무엇에 관한 이미지인지 caption(문장)을 달아주는 작업



![figure2](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/cam/grad_figure2.PNG)



- 출처 : [https://visualqa.org/](https://visualqa.org/)

### visual question answering

이미지가 무엇인지에 대한 질문이 주어질 때 질문에 대한 정답을 알아내는 작업



![figure3](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/cam/grad_figure3.PNG)



# Approach
