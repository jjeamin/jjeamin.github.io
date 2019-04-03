---
layout: post
title:  "YOLOv2,YOLO 9000"
summary: "YOLO 9000 논문 읽어보기"
date:   2019-03-23 13:00 -0400
categories: yolo
---

# YOLO 9000

- YOLO 9000 Paper : [Here](https://arxiv.org/abs/1612.08242)

---
# YOLO가 빠른 이유
- bounding box의 위치를 찾는 것과 classification이 동시에 이루어 지기 때문에 속도가 빠르다. 반면에 R-CNN 계열의 검출 네트워크들은 영상에서 오브젝트가 있을 것 같은 후보(ROI - Region Of Interest : 관심영역) 를 먼저 뽑는다. 후보로 뽑힌 ROI(영상의 작은 부분) 들은 Classification network(분류기)에 의해 클래스 분류가 이뤄지고  그 후 bounding box 를 찾는다.


# YOLO 9000

# 요약
9000가지 이상의 object detection을 수행할 수 있는 YOLOv2를 소개한다. PASCAL VOC와 COCO와 같은 표준 detection 작업에 대한 기술이다. 혁신적 multi-scale train 방법을 사용하면 속도와 정확도의 균형을 잘 유지 할 수 있다. 67FPS에서 YOLOv2는 76.8 mAP을 얻었고 40FPS에서는 78.6 mAP를 얻었다. 마지막으로 YOLO는 object detection과 classification에 대해서 jointly train(공동 훈련) 방법을 제안한다. 이 방법을 사용하여 YOLO는 9000 개 이상의 서로 다른 object의 class를 예측한다. 그리고 그것은 여전히 **실시간** 으로 진행된다.

# 소개(introduce)
일반적인 object detector은 빠르고,정확하고,다양한 물체를 인식할 수 있어야한다. Neural Net의 도입 이후 프레임 워크는 점점 더 빠르고 정확해졌다. 그러나 대부분의 detection 방법은 여전히 작은 object 집합에 제약을 받는다. 현재의 object detection 데이터 세트는 classification과 tagging 같은 다른작업을 위한 데이터 세트와 비교해 제한되어 있다. 가장 일반적인 detection 데이터 세트는 수천 수십만 개의 이미지를 포함한다. 우리는 detection이 object classification의 수준으로 확장되기를 원한다. 그러나 detection용 image labelling은 classification또는 tagging을 위한 labelling보다 expensive하다. YOLO는 이미 보유하고 있는 대량의 classification 데이터를 활용하여 현재 detection의 범위를 확장하기 위한 새로운 방법을 제안한다. 그 방법은 서로 다른 데이터 세트를 함께 결합할 수 있는 object classification의 **계층적 관점** 을 사용한다. 또한 detection 과 classification 데이터에 object detector을 훈련시킬 수 있는 joint training 알고리즘을 제안한다. YOLO의 방법은 분류된 검출 이미지를 활용해서 object의 위치를 정확하게 파악하는 동안 어휘와 견고함을 향상시키는 방법을 학습한다. 이 방법을 이용해서 9000가지가 넘는 object 카테고리를 detection 할 수 있다.

- 사전 훈련된 모델 : [HERE](http://pjreddie.com/yolo9000/)

# Better
YOLO는 최첨단 detection 시스템에 비해 다양한 단점을 가지고 있다. Fast R-CNN에 비해 YOLO는 상당한 수의 위치 파악 오류를 만든다는 것을 보여준다. 그래서 classification 정확성을 유지하면서 recall(검출율) 과 localization을 개선하는데 주력한다. 더 나은 성능은 대용량의 네트워크를 교육하거나 여러 모델을 함께 모으는 방법(앙상블)을 사용할 수 있지만 YOLOv2로 빠른 것을 유지하고 더 정확한 detector를 원한다. 네트워크를 확장하는 대신에, 네트워크를 단순화시키고 그 표현 쉽게 배울수 있도록 만든다.

## Batch Normalization
- 다른 형태의 정규화(regularzation)가 필요없게 하며 convergence를 크게 향상시킨다.
- YOLO의 모든 CNN에 batch noremalization를 추가하면 mAP에서 2% 이상의 개선 효과를 얻을 수 있다.
- 모델을 regularize하는데 도움이된다. 과적합(overfitting)없이 dropout을 제거해도 된다.

## High Resolution Classiﬁer
- 모든 최첨단 detection 방법은 ImageNet에서 pre-train된 분류자를 사용한다.
- AlexNet부터는 256x256보다 작은 입력 이미지를 처리하고 기존 YOLO는 classifier 네트워크를 224x224로 훈련시키고 탐지를 위해 448로 해상도를 증가시킨다. 이는 네트워크가 새로운 입력 해상도(resolution)을 조절하고 object detection을 학습하려고 동시에 전환해야한다는 것을 의미한다.
- YOLOv2는 먼저 ImageNet에서 10 epoch 동안 448x448 해상도로 classifier 네트워크를 fine-tuning 시킨다. 이것은 고해상도 입력 이미지에서 더 잘 작동하도록 filter를 조정할 네트워크 시간을 제공한다. 그리고 탐지 된 결과 네트워크를 fine-tuning 한다.
- 고해상도 classifier 네트워크는 거의 4% mAP가 증가했다.

## Convolutional With Anchor Boxes
YOLO는 convolution 특징 추출기 위에 fully connected layer를 사용하여 bounding box를 예측한다. 직접적으로 좌표를 예측하는 대신 Faster R-CNN은 사전에 수작업한 bounding box들을 예측한다. 단지 Faster R-CNN안의 RPN(region proposal network) 모듈에서 convolution layer들을 사용함으로써 offsets과 anchor boxes에 대한 신뢰도를 예측한다. prediction layer가 convolution이기 때문에, RPN은 feature map에서 모든 위치에서 offsets을 예측한다.

# 참조
- ssd
  + [https://m.blog.naver.com/sogangori/221007697796](https://m.blog.naver.com/sogangori/221007697796)

- yolo
  + [https://m.blog.naver.com/sogangori/220993971883](https://m.blog.naver.com/sogangori/220993971883)
  + [https://m.blog.naver.com/sogangori/221011203855](https://m.blog.naver.com/sogangori/221011203855)
