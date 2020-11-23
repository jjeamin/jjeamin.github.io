---
layout: post
title:  "EfficientDet-1"
summary: "EfficientNet 논문 읽어보기"
date:   2019-11-23 13:00 -0400
categories: paper
math: true
---

# EfficientDet

(EfficientDet: Scalable and Efﬁcient Object Detection)

- EfficientDet Paper : [Here](https://arxiv.org/abs/1911.09070)
- EfficientNet Paper : [Here](https://arxiv.org/abs/1905.11946)
- EfficientNet Official Code : [Here](https://github.com/tensorflow/tpu/tree/master/models/official/efficientnet)

# EfficientNet
EfficientNet을 보기전에 EfficientNet의 핵심 concept을 빠르게 보고 넘어가는게 좋을 것 같다.

이진원님의 [pr12 논문 읽기](https://www.youtube.com/watch?v=Vhz0quyvR7I)를 참조 했다.



![net_figure1](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/EfficientDet/net_figure1.PNG){: width="500" height="400"}{: .center}



EfficientNet은 Convolution Neural Network를 속도나 정확도 측면에서 network의 depth와 width, resolution을 조절해서 효과적이게 만들어 보자는 핵심 아이디어를 가지고 있다.

즉, `depth`, `width`, `resolution`의 scale을 조절하는 compound scaling 방법에 대한 논문이다.



![net_figure2](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/EfficientDet/net_figure2.PNG){: .center}



- `depth` : 모델의 깊이
- `width` : layer의 channels
- `resolution` : input image resolution

위에 compound scaling이 이 논문이 원하는 것이고 이것을 위해 해결해야하는 문제를 아래와 같이 정의한다.

### CNN



![net_formula1](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/EfficientDet/net_formula1.PNG){: .center}



- i : stage
- $$F_{i}^{L_i}$$ : layer $$F_i$$는 stage i에서 $$L_i$$만큼 반복된다는 뜻
- $$X$$ : input tensor
- $$H, W, C$$ : spatial dimension(w, h), chnnel dimension(c)
- example : input tensor(244, 224, 3) --> output tensor(7, 7, 512)

ResNet으로 예를들면 ResNet은 5stage가 있고 각 stage는 downsampling을 하는 것 빼고는 동일한 convolution type을 갖는다. 그래서 위와 같이 정의할 수 있는 것이다.

### Problem



![net_formula2](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/EfficientDet/net_formula2.PNG){: .center}



- hat을 붙이는 이유는 이미 정의한 base model을 사용하고 있기 때문이다.
- 연산량을 정해놓은 목표 안에서 정확성을 최대화 시키는 값을 찾는다는 뜻이다.



![net_figure3](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/EfficientDet/net_figure3.PNG){: .center}



이 그래프는 depth, width, resolution을 각자 올려보면서 성능을 평가한 것이다. 커질수록 올라가지만 어느지점에서 한계가 보인다.

## Compound Scaling



![net_figure4](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/EfficientDet/net_figure4.PNG){: .center}



이 문제를 해결하기 위해서 아래와 같이 network의 depth와 width,resolution을 조절해서 최적의 모델을 찾는 것이다.

depth: $$d = \alpha^\phi$$

width: $$w = \beta^\phi$$

resolution: $$r = \gamma^\phi$$

$$\alpha \cdot \beta^2 \cdot \gamma^2 \approx 2$$

$$\alpha \geq 1,\beta \geq 1,\gamma \geq 1$$

$$\alpha , \beta , \gamma$$는 grid search를 통해서 찾은 값.

$$\phi$$는 직접 결정하는 값

## Base Model



![net_figure5](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/EfficientDet/net_figure5.PNG){: .center}



base model 기준으로 최적의 depth, width, resolution을 찾아가는 것이다. base model은 구조를 MnasNet과 유사하게 만들었고 여기서 나오는
MBConv는 MobileNetv2에서 나온 inverted bottleneck 구조다.

처음에는 $$\phi$$값을 1로하고 $$\alpha,\beta,\gamma$$값을 grid search로 찾은 뒤 고정시키고 $$\phi$$를 조금씩 올리는 방법을 사용한다.

## Benchmark



![benchmark1](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/EfficientDet/net_benchmark1.PNG){: .center}



ImageNet에서의 performance다. 파라미터수가 기존 모델에 비해 작지만 그 이상의 성능을 가진다.



![benchmark2](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/EfficientDet/net_benchmark2.PNG){: .center}



데이터셋 별로 비교한 표

## CAM



![benchmark2](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/EfficientDet/net_cam.PNG){: .center}



기존 방법에 비해 compound scaling을 사용하면 맨 마지막 열에 있는 그림과 같이 매우 class의 형태를 잘찾는 것을 알 수 있다.
CAM에 대해서는 흥미가 있어서 포스트를 작성해볼 예정이다.


적다보니 길어져서 EfficientDet은 그 다음 포스트로 미뤄야 할 것 같다.

## Reference
- [https://www.youtube.com/watch?v=Vhz0quyvR7I](https://www.youtube.com/watch?v=Vhz0quyvR7I)
