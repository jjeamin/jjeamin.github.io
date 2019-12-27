---
layout: post
title:  "CNN Saliency Maps"
summary: "CNN Saliency Maps 논문 읽어보기"
date:   2019-12-27 13:00 -0400
categories: paper
use_math: true
---

# CNN Saliency Maps

(Deep Inside Convolutional Networks: Visualising Image Classification Models and Saliency Maps)

- paper : [https://arxiv.org/abs/1312.6034](https://arxiv.org/abs/1312.6034)

## Saliency Maps란?
Saliency Maps은 일종의 이미지를 분할이다. 어떠한 관심 영역을 추출하는 것을 말한다.

# Abstract
이 논문은 image classification model의 visualization에 대한 내용이다. input image에 대한 class score에 gradient를 계산하는 데 기반한 두 가지 시각화 기술을 기반으로 한다.

1. image를 생성해서 class score를 최대화 시키고 CNN에 의해 캡쳐 된 class의 concept을 시각화 한다.

2. 주어진 image와 class의 class saliency map을 계산한다.(weakly supervised learning에 사용할 수 있다.)

# Introduction

이전 연구에서는 image space에서 gradient ascent를 사용해 최적화를 해서 모델이 관심을 가지는 뉴런을 최대화하는 input image를 찾아서 시각화 하였다. 이 방법은 DBN(Deep Belief Network)과 같은 unsupervised 방식으로 hidden feature layers를 시각화 하는 데 사용하였고 나중에 auto-encoder로 시각화 하는데 사용된다.

*이 부분에서는 Auto Encoder를 알아가면 좋을 것 같아서 따로 포스팅을 할 것이다.*

그리고 convolutional layer의 입력을 출력에서 재구성 하는데 목적을 가지는 Deconvolutional Network 구조를 제안하였다. **convolution은 feature map의 size를 줄이지만 deconvolution은 size를 키운다.**

이 논문에서는 ImageNet pretrained model을 사용해서 시각화를 설명한다. 이 논문은 3가지 contributions를 하였다.

1. input image의 numerical optimisation(수치 최적화)를 사용해서 시각화 할 수 있다는 것을 보여준다. 이전 연구와 달리 supervised 방식을 사용하였기 때문에 최종으로 나오는 fully connected layer에서 어떤 뉴런을 최대화시켜야 할 지 알고 있다.

2. single backpropagation을 통과시켜서 image-specific class saliency map을 구하는 방법을 제안한다.

3. gradient-based 시각화 방법이 deconvolution network를 재구성하는 절차를 일반화 하는 것을 보여준다.


# Class Model Visualization

$$argmax_I S_c (I) - \lambda \left \| I \right \|^2_2$$

- $$c$$ : class
- $$I$$ : image
- $$S_c (I)$$ : class의 score
- $$\left \| \right \|^2_2$$ : L2 regularised
- $$\lambda$$ : regularisation parameter

backpropagation을 이용해서 locally optimal $$I$$를 찾을 수 있다. 즉, pretrained model에서 input image에 대해서 최적화가 이루어지고 weights는 고정된다. zero image로 optimisation을 초기화하고 train datasets의 평균 image를 결과와 더한다.

softmax를 사용하지 않고 class score를 사용한다. 왜냐하면 다른 class의 score를 최소화해서 posterior의 최대화 시키기 때문이다. 그래서 $$S_c$$만 최적화 한다.



![figure](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/saliency/figure.PNG){: .center}



# Image-Specific Class Saliency Visualisation

In this section we describe how a classification ConvNet can be queried about the spatial support of a particular class in a given image.

$$S_c(I) = w^T_c I + b_c$$

score의 영향을 기준으로 image의 pixel에 대한 순위를 매길 수 있다. image는 1차원으로 표현된다. 이 경우 weights가 image pixel의 중요성을 정의한다는 것을 알 수 있다.

하지만 깊은 CNN 같은 경우 score는 비선형 함수다. 그래서 위의 공식을 바로 적용할 수 없다. 그치만 image가 주어지면 score를 근사할 수 있다.(Taylor expansion)

$$S_c(I) \approx w^T I + b_c$$

여기서 $$w$$는 image에 대한 score의 미분이다.

$$w = \frac{\partial S_c}{\partial I} $$

이에 대한 해석은 도함수의 크기가 class score에 가장 영향을 미치기 위해 가장 적게 변경되어야하는 pixel을 나타낸다는 것이다.

## Class Saliency Extraction

Saliency Maps를 계산하려면 먼저 weights를 backpropagation을 통해서 구한다. 그리고 weights를 재배열해서 Saliency Maps을 얻는다.($$M \in R^{m \times n}$$)

gray image의 경우 weights수와 image의 pixel수와 동일하기 때문에 $$M_{ij} = \left | w_{h(i,j)} \right |$$ 로 계산한다. 여기서 $$h(i, j)$$는 i번째 행과 j번째 열의 image pixel에 해당하는 weights의 index다.

RGB image의 경우는 단일 class의 Saliency Maps를 얻기 위해서 모든 channels에 대한 최댓값을 취한다. 즉, $$M_{ij} = max_c \left | w_{h(i, j, c)}\right |$$



![figure1](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/saliency/figure1.PNG){: .center}



## Weakly supervised Object Localisation

Saliency Maps은 image의 위치를 encoding하기 때문에 object의 위치를 찾는 곳에서도 사용할 수 있다.

colour segmentation을 사용하면 Saliency Maps이 object와 가장 차별적인 부분만 얻으려고 하기 때문에 saliency thresholding으로는 전체 object를 나타내기 어려울 수 있다. 따라서 thresholded map을 object의 다른 부분으로 전파 할 수 있어야 하기 때문에 colour continuity cues를 사용한다.



![figure2](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/saliency/figure2.PNG){: .center}



- foreground model은 saliency가 threshold 보다 높은 pixel로 추정 되었고 saliency 분포의 95%보다 큰 pixel을 선택한다.

- background model은 saliency사 30%보다 작은 pixel을 선택한다.

그런다음 `GraphCut segmentation`을 사용했다. foreground와 background가 계산되면 foreground에서 가장 연결이 크게 된 component로 설정된다.

**GraphCut Segmentation**



![figure3](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/saliency/figure3.PNG){: .center}



- paper : Interactive graph cuts for optimal boundary and region segmentation of objects in N-D images

pixel을 Graph로 연결해서 cut해서 segmentation 하는 방법론

# Relation to Deconvolutional Networks

Gradient 기반 시각화 기술이 DeconvNet의 재구성 절차를 일반화 하는 것을 보여준다.

*논문을 참조*

# Conclusion
2가지 시각화 기법을 제시하였다.

1. 특정 class를 나타내는 이미지를 생성하는 방법
2. Saliency maps을 계산하여 영역을 강조하는 방법

# Reference
- [https://github.com/utkuozbulak/pytorch-cnn-visualizations/tree/4473bc24276d13f8b64088087257045938da5f4c](https://github.com/utkuozbulak/pytorch-cnn-visualizations/tree/4473bc24276d13f8b64088087257045938da5f4c)
