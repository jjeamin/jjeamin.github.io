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

---

# YOLO 9000

# 요약
9000가지 이상의 object detection을 수행할 수 있는 YOLOv2를 소개한다. PASCAL VOC와 COCO와 같은 표준 detection 작업에 대한 기술이다. 혁신적 multi-scale train 방법을 사용하면 속도와 정확도의 균형을 잘 유지 할 수 있다. 67FPS에서 YOLOv2는 76.8 mAP을 얻었고 40FPS에서는 78.6 mAP를 얻었다. 마지막으로 YOLO는 object detection과 classification에 대해서 jointly train(공동 훈련) 방법을 제안한다. 이 방법을 사용하여 YOLO는 9000 개 이상의 서로 다른 object의 class를 예측한다. 그리고 그것은 여전히 **실시간** 으로 진행된다.

---

# 소개(introduce)
일반적인 object detector은 빠르고,정확하고,다양한 물체를 인식할 수 있어야한다. Neural Net의 도입 이후 프레임 워크는 점점 더 빠르고 정확해졌다. 그러나 대부분의 detection 방법은 여전히 작은 object 집합에 제약을 받는다. 현재의 object detection 데이터 세트는 classification과 tagging 같은 다른작업을 위한 데이터 세트와 비교해 제한되어 있다. 가장 일반적인 detection 데이터 세트는 수천 수십만 개의 이미지를 포함한다. 우리는 detection이 object classification의 수준으로 확장되기를 원한다. 그러나 detection용 image labelling은 classification또는 tagging을 위한 labelling보다 expensive하다. YOLO는 이미 보유하고 있는 대량의 classification 데이터를 활용하여 현재 detection의 범위를 확장하기 위한 새로운 방법을 제안한다. 그 방법은 서로 다른 데이터 세트를 함께 결합할 수 있는 object classification의 **계층적 관점** 을 사용한다. 또한 detection 과 classification 데이터에 object detector을 훈련시킬 수 있는 joint training 알고리즘을 제안한다. YOLO의 방법은 분류된 검출 이미지를 활용해서 object의 위치를 정확하게 파악하는 동안 어휘와 견고함을 향상시키는 방법을 학습한다. 이 방법을 이용해서 9000가지가 넘는 object 카테고리를 detection 할 수 있다.

- 사전 훈련된 모델 : [HERE](http://pjreddie.com/yolo9000/)

---

# Better
YOLO는 최첨단 detection 시스템에 비해 다양한 단점을 가지고 있다. Fast R-CNN에 비해 YOLO는 상당한 수의 위치 파악 오류를 만든다는 것을 보여준다. 그래서 classification 정확성을 유지하면서 recall(검출율) 과 localization을 개선하는데 주력한다. 더 나은 성능은 대용량의 네트워크를 교육하거나 여러 모델을 함께 모으는 방법(앙상블)을 사용할 수 있지만 YOLOv2로 빠른 것을 유지하고 더 정확한 detector를 원한다. 네트워크를 확장하는 대신에, 네트워크를 단순화시키고 그 표현 쉽게 배울수 있도록 만든다.


## Batch Normalization
- 다른 형태의 정규화(regularzation)가 필요없게 하며 convergence를 크게 향상시킨다.
- YOLO의 모든 CNN에 batch noremalization를 추가하면 mAP에서 2% 이상의 개선 효과를 얻을 수 있다.
- 모델을 regularize하는데 도움이된다. 과적합(overfitting)없이 dropout을 제거해도 된다.

---

## High Resolution Classiﬁer
- 모든 최첨단 detection 방법은 ImageNet에서 pre-train된 분류자를 사용한다.

- AlexNet부터는 256x256보다 작은 입력 이미지를 처리하고 기존 YOLO는 classifier 네트워크를 224x224로 훈련시키고 탐지를 위해 448로 해상도를 증가시킨다. 이는 네트워크가 새로운 입력 해상도(resolution)을 조절하고 object detection을 학습하려고 동시에 전환해야한다는 것을 의미한다.

- YOLOv2는 먼저 ImageNet에서 10 epoch 동안 448x448 해상도로 classifier 네트워크를 fine-tuning 시킨다. 이것은 고해상도 입력 이미지에서 더 잘 작동하도록 filter를 조정할 네트워크 시간을 제공한다. 그리고 탐지 된 결과 네트워크를 fine-tuning 한다.

- 고해상도 classifier 네트워크는 거의 4% mAP가 증가했다.

---

## Convolutional With Anchor Boxes

- YOLO는 convolution 특징 추출기 위에 fully connected layer를 사용하여 bounding box를 예측한다. 직접적으로 좌표를 예측하는 대신 Faster R-CNN은 사전에 수작업한 bounding box들을 예측한다.

- Faster R-CNN안의 RPN(region proposal network) 모듈에서 convolution layer들을 사용함으로써 offsets과 anchor boxes에 대한 신뢰도를 예측한다. prediction layer가 convolution이기 때문에, RPN은 feature map에서 모든 위치에서 offsets을 예측한다. 좌표 대신 offsets을 예측하면 문제가 간단해지고 네트워크 학습을 더 쉽게 할수 있다.

- fully connected layers를 YOLO에서 제거하고 bounding box를 예측하기 위해 anchor boxes를 사용한다. 먼저 output의 convolution layer를 높은 해상도로 만들기 위해 하나의 pooling layer를 제거한다.

- 448x448 대신 416 입력 이미지에서 작동하기 위해 네트워크를 줄인다. -> 최종 feature map을 홀수x홀수로 만들기 위해서이다. -> 홀수 이므로 단일 center cell이 존재할 수 있다. 짝수는 center가 4개 이다. 특히 큰 object는 image의 중심을 차지 하는 경향이 있으므로 feature map의 center가 4개로 잡힌다면 object를 검출하는데 어려움이 생길수 있다.

- YOLO의 convolution layer는 image를 32배만큼 downsampling하므로 416 입력 이미지를 사용해 13x13의 feature map을 얻는다.

- anchor box를 사용하면서 공간 위치로부터의 class 예측 매커니즘도 분리시키고 그것이 object일 확률을 예측하며 classification 한다. 예측된 bounding box가 object일때 그것이 어떤 class인지를 예측하기 때문에 **조건부 확률** 이 된다. anchor box를 사용하면 정확성이 약간 감소하지만 천개 이상의 예측을 할 수 있다.

- no anchor box = `mAP : 69.5`,`recall(검출율) : 81%``
- anchor box = `mAP : 69.2`,`recall(검출율) : 88%`

---

## Dimension Cluster



![cluster](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/yolo/cluster.PNG)



YOLO에서 anchor box를 사용할 때 두 가지 문제점을 갖는다.

- 첫번째, box dimension을 손수 설정 해야하는 것이다. 이전에는 직접 설정 했지만 YOLO는 자동적으로 bounding box를 학습에 적용하기 위해 k-means Clustering 알고리즘을 사용한다.
만약 표준 k-means를 유클리드 거리(점과 점사이의 거리)에서 사용한다면, 큰 box는 작은 box보다 많은 에러가 발생한다. 그러나 box의 크기와 상관없이 좋은 IOU를 이끄는 것을 원한다. 그러므로 거리 행렬을 위에 다음 식을 사용한다.



![dm](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/yolo/dm.PNG)



- 위에 그림을 보면 알 수 있듯이 k의 다양한 값을 위해 k-means를 작동하고 가장 가까운 IOU 평균을 표시한다. k가 커지면 clustering결과와 label사이의 IOU가 커져서 recall이 좋아지지만 모델의 복잡도가 상승하는 trade-off 관계가 있기 때문에 k값으로 5를 선택했다.

- **우선 순위의 anchor box를 선택하는 Clustering 전략과 직접선택하는 anchor box를 비교해보면 전자가 더 좋은 결과가 나옵니다.**

---

## Direct location prediction
- anchor box를 사용하면 학습 초기에 모델이 불안정해지는 문제를 해결할 수 있었는데 학습이 불안정한 원인은 box의 좌표가 랜덤하게 예측되기 때문이다. RPN에서 네트워크는 tx와 ty를 예측하고 (x,y)의 중앙 좌표는 아래 식에 의해서 계산된다.



![dm](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/yolo/dlp.PNG)



- tx,ty는 convolution연산의 출력 값이다.

- 예를 들어, tx=1이라면 box를 오른쪽으로 이동시킬 것이고 tx=-1이라면 왼쪽으로 이동할 것이다. 이 공식은 제한되지 않는다. 이 공식은 제약이 없으므로 box를 예측 한 위치에 관계 없이 anchor box의 위치가 영상 어디에도 나타날 수 있다.

- offsets을 예측하는 대신에 grid cell의 위치와 상대적인 위치 좌표들을 예측한다. x,y의 위치가 grid cell 내부에만 있도록 제약을 두었기 때문에 ground truth가 0~1의 값을 갖게된다. 그리고 예측할때는 logistic activation을 사용해서 0~1의 값을 갖게한다.

- 네트워크는 output인 feature map에서의 각각의 cell에서 5개의 bounding box를 예측한다. 각 bounding box에 tx,ty,tw,ty,to에 대한 5개의 좌표를 예측한다.



![dm](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/yolo/dlp2.PNG)



- `cx,cy` : 각 `grid cell`의 좌상단 끝에 offset
- `pw,ph` : 우선 순위 anchor box의 width,height
- `tx,ty` : `sigmoid` 함수를 거친다.
- `bx = sigmoid(tx) + cx`에서 `tx`가 0값을 가지면 `bx`는 중심 좌표 x
- `by = sigmoid(ty) + cy`에서 `ty`가 0값을 가지면 `bx`는 중심 좌표 y

즉, tx와 ty는 0의 값을 가지기를 원한다. 또한 `tw,th`도 학습과정에서 0에 가까운 값이 되기를 원한다.

- `bw = pw*e^(tw)`에서 `tw`가 0값을 가지면 `bw = pw`
- `bh = ph*e^(th)`에서 `th`가 0값을 가지면 `bh = ph`

- 위치 예측 파라미터에 sigmoid 함수, 경계박스의 중심이 grid cell 안에 있어야한다는 제약이 있기에 안정적이게 학습하고, Dimension Cluster를 사용했을때 mAP가 5% 상승한다.

---

## Fine-Grained Features

- YOLOv2는 13x13 feature map 을 예측한다. 큰 object에서 충분한 반면, 더 작은 object들의 localizing하는 것 보다 세밀한 특징들로부터 효과를 얻을수 있다.

- Faster R-CNN이나 SSD는 네트워크의 다양한 feature map에서 그들이 Proposal Network(PN)를 실행해 해결방안을 제시한다. YOLOv2는 26x26 해상도의 이전 layer의 기능을 가져오는 **passthrough** layer만 추가하므로 다르게 접근한다.

- passthrough layer는 ResNet의 identity mapping과 유사하게 고해상도 feature와 저해상도 feature를 연결한다. 26x26x512 feature map은 13x13x2048 feature map으로 변경하고, 기존의 feature들과 연결할수 있다. -> 1% 향상

---

## Multi Scale Trining










# 추가 용어

## RPN(region proposals network)
- RPN은 지역을 제안하는 네트워크라는 의미로 sliding window에 anchor box를 제안하는 네트워크이다.

- image에서 cnn으로 feature map을 찾고 feature map을 input으로 RPN에서 미리 학습된 anchor box를 이용해 후보영역을 추출하고 classifier과 regression 네트워크를 거쳐서 object인지 background인지, anchor당 **델타값** 들(x,y,w,h)를 내놓고 이 델타값들을 anchor에 적용이 되어서 최종적인 output을 얻는다.

- 델타값 : `x - x'`

## anchor box
- Faster R-CNN에서 언급된 용어로 Bounding box의 후보로 사용되는 상자다. RPN은 sliding window를 사용한다. sliding window는 이미지위에서 직접 지정한 window를 옮겨가는 알고리즘이다. window의 위치를 중심으로 **사용자가 정의한 다양한 크기의 anchor box** 들을 적용해 feature를 추출하는데 image의 크기를 조정할 필요없고,filter 크기를 변경할 필요가 없기 때문에 효율이 좋다.

## IOU
- intersection over union(합집합 over 교집합) : 실제 bounding box와 예측 bounding box가 얼마나 잘 겹쳐지는지

## passthrough

# 참조
- ssd
  + [https://m.blog.naver.com/sogangori/221007697796](https://m.blog.naver.com/sogangori/221007697796)

- yolo
  + [https://m.blog.naver.com/sogangori/220993971883](https://m.blog.naver.com/sogangori/220993971883)
  + [https://m.blog.naver.com/sogangori/221011203855](https://m.blog.naver.com/sogangori/221011203855)

- Faster R-CNN
  + [https://curt-park.github.io/2017-03-17/faster-rcnn/](https://curt-park.github.io/2017-03-17/faster-rcnn/)
