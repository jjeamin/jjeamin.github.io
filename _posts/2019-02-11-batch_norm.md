---
layout: post
title:  "Batch Normalization"
summary: "Batch Normalization이란??"
date:   2019-02-11 11:10 -0400
categories: DNN
---

## Tensorflow
```python
bn = tf.layers.batch_normalization(layer, training=training)
```

## Batch Normalization 이란??
### 딥러닝의 문제!
- **parameter** 가 많다.
- weight의 변화가 중첩되어 가중되는 크기 변화가 크다.
-> <span style="color:red"> internal covariate shift </span>


### 해결을 위한 노력
- careful initialization : <span style="color:red">Difficult</span>
- small learning rate : <span style="color:red">Slow</span>

### Batch Normalization
- **weight가 변하는 범위가 작을수록 학습이 잘될 것이다.**
- **활성화 함수 전에 적용시킨다.**
- 평균 : 0, 분산 : 1
- 활성화 함수

**relu**
+ 0 이하 : 0에 수렴
+ 0 이상 : non-linear
+ 비대칭적인 부분을 많이 사용할 수 있다.

**sigmoid**
+ vanishing gradient 문제 -> 0 주변만 사용시키기
+ scale 범위 조정, Shift

##### 장점
- internal covariate shift 문제가 없다
- learning rate를 크게 잡아도 된다.
- 초기값을 신중하게 잡지않아도 된다.
- dropout을 쓸필요가 딱히 없다.

# 참조
[https://www.youtube.com/watch?v=TDx8iZHwFtM&index=22&list=PLlMkM4tgfjnJhhd4wn5aj8fVTYJwIpWkS](https://www.youtube.com/watch?v=TDx8iZHwFtM&index=22&list=PLlMkM4tgfjnJhhd4wn5aj8fVTYJwIpWkS)
