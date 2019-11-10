---
layout: post
title:  "RetinaFace"
summary: "RetinaFace 논문 읽어보기"
date:   2019-11-08 13:00 -0400
categories: paper
use_math: true


---

# RetinaFace

(RetinaFace: Single-stage Dense Face Localisation in the Wild)

- RetinaFace Paper : [Here](https://arxiv.org/abs/1905.00641)
- Official Code : [Here](https://github.com/deepinsight/insightface/tree/master/RetinaFace)

# WIDER FACE
2019 WIDER FACE : [https://wider-challenge.org/2019.html](https://wider-challenge.org/2019.html)
DataSet : [http://shuoyang1213.me/WIDERFACE/](http://shuoyang1213.me/WIDERFACE/)

face detection challenge의 일종으로 얼굴 검출뿐만 아니라 보행자 검출, 같은 사람 찾기, 언어로 사람 찾기와 같은 challenge가 있다.

dataset에는 32,203개의 이미지와 393,703개의 bounding box가 있다. bounding box의 형태는 [left, top, width, height, score] 형태로 되어있다.

Easy Set, Medium Set, Hard Set으로 나누어져 있고 여러가지 환경(scale, pose, occlusion, expression, makeup, illumination)에 대한 데이터가 들어있다.

# Abstract
얼굴을 인식하는 분야는 매우 엄청난 발전이 이루어졌다. 하지만 사람의 얼굴이 너무 많거나 밀집되어있는 경우에 정확하고 효율적인 얼굴 위치를 파악하는 것은 매우 어려운 과제다.

RetinaFace는 self-supervised multi task learning과 joint extra-supervised의 장점을활용해서 다양한 scale의 얼굴의 위치를 파악한다.

- WIDER FACE 데이터 셋에 5개의 얼굴 랜드 마크에 주석을 달았다.

- 성능이 뛰어나다. 기존의 최첨단 AP보다 1.1% 높은 성능을 가진다.

- `light-weight backbone network`를 사용해서 단일 CPU에서 실시간으로 실행가능하다.

- 얼굴의 3D정보를 예측하기 위한 self-supervised mesh decoder branch를 추가했다.

# Introduce
얼굴의 위치를 찾는 작업은 얼굴의 표정, 나이를 찾는 것과 같은 많은 application에서 필수 단계다.

이 논문에서는 face detection, face alignment, pixel wise face parsing, 3D dense correspondence regression과 같은 개념을 적용한다.

face detection은 일반적인 object detection과 달리 features(눈,코,입) 비율의 변화가 적지만(1:1 ~ 1:1.5) 훨씬 더 큰 scale의 변화를 가진다(몇개 픽셀 ~ 수천개의 픽셀)

 feature pyramid 구조를 가지고 single stage에 중점을 둔다.



![figure1](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/retinaface/figure1.PNG



위의 그림이 retinaface의 전체적인 생각이다.

mesh decoder는 joint 모양과 질감에 대한 Graph convolution을 활용해 실시간 속도를 달성하는 방법

mesh decoder를 single stage에 적용하려면 특정한 작업을 해야한다.
- camera parameter를 찾기 어렵다.
- ROI pooling 된 feature 대신 단일 feature vector(feature pyramid에서 1x1 conv)에서 joint의 숨겨져있는 모양과 질감이 예측된다. 이것이 feature shift 위험을 나타낸다.

mesh decoder paper : [https://arxiv.org/abs/1904.03525](https://arxiv.org/abs/1904.03525)

# RetinaFace

## Multi-task Losss

- $$i$$ : anchor

$$
L = L_{cls}(p_i,p_i^*)) + \lambda_1 p_i^* L_{box}(t_i,t_i^*) + \lambda_2 p_i^* L_{pts}(l_i,l_i^*) + \lambda_3 p_i^*L_{pixel}
$$

### classification
- $$p_i$$ : i가 얼굴이라고 예측된 확률
- $$p_i^*$$ : positive anchor면 1 , negative anchor면 0
- $$L_{cls}$$ : binary class를 위한 softmax loss

### box regression
- $$L_{box}(t_i,t_i^*)$$ : box regression loss
- $$t_i = \left \{  t_x,t_y,t_w,t_h\right \}$$
- $$t_i^* = \left \{  t_x^*,t_y^*,t_w^*,t_h^*\right \}$$

positive anchor와 관련된 5개의 예측/실측 box coordinates

smooth-L1 loss를 사용한다.

### landmark regression
- $$L_{pts}(l_i,l_i^*)$$
- $$l_i = \left \{  l_{x1},l_{y1},...,l_{x5},l_{y5}\right \}$$
- $$l_i^* = \left \{  l_{x1}^*,l_{y1}^*,...,l_{x5}^*,l_{y5}^*\right \}$$

positive anchor와 관련된 5개의 예측/실측 landmark

### dense regression
- $$L_{pixel}$$ : Dense regression loss
- $$\lambda_1 , \lambda_2 , \lambda_3$$ : 0.25, 0.1, 0.01

즉, supervision signals에서 better box와 landmark location의 중요성을 높인다.

## Dense Regression Branch

### Mesh Decoder
mesh decoder를 직접 사용하는데, 이것은 fast localised spectral filtering을 기반으로 하는 Graph convolution 방식이다.



![figure3](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/retinaface/figure3.PNG



2D convolution 연산은 euclidean grid receptive field내에서 "kernel-weighted neighbour sum"이다.



![euclidean](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/retinaface/euclidean.PNG



위에 녹색선이 euclidean distance니까 저 위에 grid 영역의 합이라고 정의한거 같다..?

유사하게 Graph convolution도 오른쪽 그림과 같은 개념을 사용한다. 그러나 인접한 거리는 두 정점을 연결하는 최소 edge의 수를 계산하여 Graph에서 게산된다. 이 개념을 사용한 논문에서 $$G=(V,E)$$를 정의한다. 여기서 V는 정점이고 E는 edge다.
