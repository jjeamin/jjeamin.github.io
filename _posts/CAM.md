---
layout: post
title:  "CAM"
summary: "CAM 논문 읽어보기"
date:   2019-12-02 13:00 -0400
categories: paper
use_math: true
---

# CAM

(Learning Deep Features for Discriminative Localization)

- CAM paper : [Here](https://arxiv.org/abs/1512.04150)
- Demo : [Here](http://places.csail.mit.edu/demo.html)

# Abstract
- GAP(global average pooling)을 제안하는 논문
- Object Localization
- bounding box나 segmentation을 label로 주고 학습을 하지 않아도 이미지에서 차별적인 어떠한 영역을 localization 할 수 있다.



![figure1](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/cam/figure1.PNG)



# Introduction

Convolution layer에서는 object를 localization 할 수 있는 능력을 가지고 있지만, fully connected layers가 classification에 사용될 때 이러한 능력이 사라진다.

GoogleNet과 같은 fully convolutional network는 성능을 유지하면서 파라미터 수를 줄이기 위해 fully connected layer를 사용하지 않고 global average pooling을 사용한다.

이 논문에서는 global average pooling이 단순하게 정규화 하는 역할 뿐 아니라 약간의 조절로 네트워크는 마지막 layer까지 localization을 할 수 있는 능력을 유지 할 수 있다.

## Weakly supervised object detection

기존의 supervised learning object detection은 bounding box가 정답으로 주어진 상태로 학습을 해서 위치와 클래스를 예측하는 학습 방법이었다면 weakly supervised object detection은 예를 들어 고양이 있는 사진 수만장을 고양이가 있다고만 알려주고 학습을 반복시키면 예측할 고양이 사진의 위치를 찾을 수 있는 학습 방법이다.


# Class Activation Mapping



![figure2](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/cam/figure2.PNG)



GoogleNet과 유사한 네트워크를 사용하고 마지막 layer 바로 전에 GAP(global average pooling)을 수행한다. 그리고 마지막 layer의 가중치를 convolution feature map에 투영시켜서 이미지의 특정 영역의 중요성을 판별할 수 있다.

위에 그림 처럼 GAP는 마지막 convolution layer에서 각 feature map의 spatial average를 출력한다. 이러한 값의 weighted sum은 최종 출력을 생성하는데 사용된다.

## Global Average Pooling

각 feature map의 평균값을 구한다.



![gap](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/cam/gap.PNG)



$$f_k(x,y)$$는 마지막 convolution layer의 feature map에서 $$k$$번째 unit(channel)의 위치 $$(x,y)$$의 activation을 나타낸다.

$$F^k$$는 GAP을 수행한 결과이며 즉, $$\sum_{x,y} f_k(x,y)$$다.
