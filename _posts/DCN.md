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
