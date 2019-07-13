---
layout: post
title:  "Xception MobileNet"
summary: "Xception MobileNet"
date:   2019-07-07 13:00 -0400
categories: paper
---

**수행능력을 향상시키기 위한 최고의 방법 : 모델의 깊이를 늘린다.**
  + parameter가 늘어남에 따라서 overfitting이 발생할수 있다.
  + 계산량이 매우 많이 늘어난다.

# Xception

` stack of depthwise separable convolution with residual connections`

Xception은 Inception에 기초를 두고 있고 sparse하게 네트워크의 구조를 생각하는데 중점을 둔다. 일반적인 convolution은 3차원 필터를 사용해 모든 correlation을 파악하려고 한다. 그래서 이를 sparse하게 해서 correlation을 더 잘 학습시키려는 것이다.

# Inception Module



![simple](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/xception/simple.PNG)



위와 아래는 같다.



![simple2](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/xception/simple2.PNG)



- `1x1 conv` : 채널 간의 상관관계
  + 차원 축소 (Dimension reduction)
  + 표현력이 증가(Increase the representational power)

- `3x3 conv` : 공간, 방향 상관관계

# Strong Inception Module



![dsc](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/xception/dsc.PNG)



각 output channel 마다 spatial convolution을 적용했는데 이게 마치 `depthwise separable convolution`와 유사하다.

### depthwise separable convolution
위와 같이 강력하게 만든 module과 `depthwise separable convolution`의 차이점은

1. layer의 순서 : depthwise : `3x3 -> 1x1` xception `1x1 -> 3x3`
2. Non-linear : 활성화 함수를 취하지 않는다.

### skip connection

resnet에서 사용되는 지름길을 만들어주는 기술이라고 생각하면 된다. 네트워크의 입력과 출력을 더해서 그 다음 layer에 입력으로 사용하는 것인데, 학습이 잘되고 속도가 향상되는 장점이 있다.

# 모델


![model](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/xception/model.PNG)



# 결론
`depthwise separable convolution`과 유사한 Strong Inception module을 만들었으며 그에 따라서 Inception v3 보다 성능이 좋아졌다.

---
# MobileNet
- Google에서 만든 논문



---

# 참조
- [MobileNet](https://www.youtube.com/watch?v=V0dLhyg5_Dw)
- [Xception](https://www.youtube.com/watch?v=V0dLhyg5_Dw)
- [https://norman3.github.io/papers/docs/google_inception.html](https://norman3.github.io/papers/docs/google_inception.html)
- [https://gamer691.blogspot.com/2019/02/paper-review-xception-deep-learning.html](https://gamer691.blogspot.com/2019/02/paper-review-xception-deep-learning.html)
