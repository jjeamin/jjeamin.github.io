---
layout: post
title:  "DCN"
summary: "Deformable Convolutional Networks 논문 읽어보기"
date:   2019-11-13 13:00 -0400
categories: paper
use_math: true
---

# DCN

(Deformable Convolutional Networks)

- DCN Paper : [Here](https://arxiv.org/abs/1703.06211)
- Official Code : [Here](https://github.com/msracver/Deformable-ConvNets)

# Abstract

*Convolutional neural networks (CNNs) are inherently limited to model geometric transformations due to the ﬁxed geometric structures in their building modules.*

CNN은 고정적인 기하학적 구조로 인해서 모델의 기하학적 변환이 본질적으로 제한된다.

이 논문에서는 deformable convolution 과 deformable RoI pooling을 제안한다. 두 가지 방법 모두 추가적인 supervision없이 offsets을 학습하고 추가적인 offsets으로 spatial sampling location을 확장한다. **위에 제안한 모듈(deformable)은 일반 모듈(normal)을 쉽게 대체할 수 있고 backpropagation도 쉽게 할 수있다.**

object detection과 semantic segmentation과 같은 vison task에 효과적이다.

기하학적 변환 : 같은 이미지 내의 object는 여러가지 스케일이나 형태를 가질 수 있다. 이러한 같은 공간(이미지) 내의 형태 변환??

# Introduce
visual recognition의 주요 과제는 object scale, pos, viewpoint, part deformation에서의 geometric transformations, geometric variations를 적응하는 방법이다.

일반적으로 2가지 방법이 있다.

1. 원하는 변형이 충분히 있는 training datasets을 만든다.

이것은 일반적으로 image를 augmentation 하는 방법이 있다. 효과가 좋지만 이미지를 augmentation해서 훈련하는데 시간이 많이들고 설정해야하는 parameter가 많아진다.

2. transformation invariant features 과 알고리즘을 사용한다.

`SIFT`나 sliding window 기반의 object detection과 같은 잘 알려진 기술이 포함된다.

### SIFT
- Scale-Invariant Feature Transform
- 이미지의 변화(크기와 회전)에 대해 불변하는 특징을 추출하는 알고리즘



![sift](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/dcn/sift.jpg)



- 매우 복잡한 변화에는 약하다.

자주 사용되는 CNN은 고정 된 위치에서 feature map을 sampling한다. Pooling은 고정 된 비율로 spatial resolution을 감소시키고, ROI Pooling은 고정 된 spatial bins로 분리한다. 기하학적 변환을 처리하기위한 내부 매커니즘이 없다.

고정 된 위치 말고 서로 다른 위치를 가진다면 여러 scale과 변형을 갖는 object에 대응할 수 있기 때문에, scale과 receptive field 크기를 적응적으로 결정하는 것이 좋다.

그래서 deformable convolution과 deformable RoI Pooling을 소개한다.



![figure1](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/dcn/figure1.jpg)



먼저 deformable convolution을 살펴보면 offsets은 추가적인 convolution layer를 통해 이전 feature map에서 학습된다. 따라서 deformation은 input feature 에 국부적이고 조밀하며 적응적 방식으로 조절된다.

그다음 deformable Roi Pooling은 이전 Roi Pooling에 bin partition에서 각 bin 위치에 offsets을 추가한다. 아래 그림을 보면 이해하기 쉬울 것이다.

# Deformable Convolution Networks



![figure2](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/dcn/figure2.jpg)



## Deformable Convolution
2D convolution은 2단계로 구성된다.

1. 입력 feature map x에 대해 정규 그리드 $$R$$을 사용해서 샘플링
2. w에 의해 가중된 샘플링 된 값의 합산, 그리드 $$R$$은 수용 가능한 size 및 dilation을 정의한다. 예를 들어

$$R = \left \{ \left ( -1,1 \right ), \left ( -1,1 \right ),...,\left ( -1,1 \right ), \left ( -1,1 \right )  \right \}$$

dilation 1, 3x3 kernel로 정의한다.

### convolution

$$y(p_0) = \sum_{p_n \in R}w(p_n) \cdot x(p_0 + p_n)$$

- $$p_0$$ : output feature map y에서 위치
- $$p_n$$는 $$R$$의 위치값들

### deformable convolution

$$y(p_0) = \sum_{p_n \in R}w(p_n) \cdot x(p_0 + p_n + \Delta p_n)
$$

- $$\Delta p_n$$ : offsets 일반적으로 분수다. 위 식은 bilinear interpolation으로 구현된다.

- $$x(p) = \sum_q G(q,p) \cdot x(q)$$

- $$p$$ : $$p_0 + p_n + \Delta p_n$$

- $$q$$ : feature map x에서 모든 필수적인 공간적인 위치를 순회하는 변수

- $$G(·,·)$$ : bilinear interpolation kernel 이다. 2차원이다. 2개의 1차원 kernel로 분리되어있다.

$$G(q,p) = g(q_x,p_x) \cdot g(q_y,p_y)$$

- $$g(a,b) = max(0,1-|a - b|)$$

최종적으로 convolution을 위한 kernel을 유동적으로 변하게 하기위해서 offsetss field를 학습시킨다.

## Deformable Roi Pooling
임의의 크기의 영역을 고정 크기의 feature map으로 변환하는 작업을 한다.



![figure3](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/dcn/figure3.jpg)



### Roi Pooling
k x k개의 bins으로 나누고 k x k의 feature map y를 출력한다.

$$y(i,j) = \sum_{p \in bin(i,j)} x(p_0 + p) / n_ij$$

- $$n_ij$$ bin안에 있는 pixel의 수
