---
layout: post
title:  "SSD"
summary: "SSD 논문 읽어보기"
date:   2019-08-21 13:00 -0400
categories: paper
---

# SSD

(Single Shot MultiBox Detector)

- SSD Paper : [Here](https://arxiv.org/abs/1512.02325)
- Official code : [Here](https://github.com/weiliu89/caffe/tree/ssd)

# Abstract
bounding box의 output 공간을 나누고 각 feature map에서 서로 다른 종횡비로 default box를 생성하고 예측시에 default box에 score를 구해서 box를 조절한다.

- subsequent pixel과 resampling 제거 : **단순함**
- single stage detector

# Introduce

## `VOC2007`
- `300x300` : 77.2% mAP
- `512x512` : 79.8% mAP

**resampling을 하지 않기 때문에** YOLO와 Faster-RCNN보다 속도와 성능이 좋다. 작은 convolution filter를 사용해 bounding box의 위치에서 object categories와 offsets을 예측하고 다양한 종횡비를 detection하기 위해서 별도의 filters를 사용했고 다양한 scale에 detection을 하기 위해 다양한 feature map에 적용하기도 하였다.

`YOLO`의 경우 `63.4 % mAP`에서 `SSD`의 경우 `74.3 % mAP`로 향상된다.

# The Single Shot Detector



![SSD](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/ssd/feature.PNG)



여러가지 feature map에서 학습하고 default box와 ground truth box를 일치 시키고 일치된 box를 양성 나머지를 음성으로 정의한다.

- loss : localization loss + confidence loss

# Model
고정된 크기의 bounding box들과 해당 box에 object class의 점수를 예측하는 feed-forward convolutional network를 기반으로 한다.



![model](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/ssd/model.PNG)



## Multi-scale feature maps for detection
VGG-16를 부분만 사용하고 그 뒤에 feature를 추출하는 feature map을 추가한다. grid의 크기는 점차 감소하고 다양한 scale에서 object를 예측할 수 있다.

`38x38`, `19x19`, `10x10`, `5x5`, `3x3`, `1x1`

## Convolutional predictors for detection  
각 feature를 추출하는 layer마다 `3x3xp` kernel을 가지는 convolution layer를 연결시켜서 bounding box의 offset과 class,confidence를 예측한다.

`p = 바운딩박스 개수 x (class score + offset)`

## Default boxes and aspect ratios
default box와 feature map을 각 grid cell과 연관시킨다. 각각의 feature map에서는 cell의 default box로 오프셋을 예측하고 이로 인해서 총 `(c + 4) * k` 개의 필터가 생기고 `m x n feature map`에 대해서 `(c + 4) * kmn` 의 output이 생긴다.
