---
title:  "LightGBM 그리고 XGBoost"
summary: "LightGBM 그리고 XGBoost"
date:   2021-06-29 09:10 -0400
categories: machinelearning
---

> Competitions에서 맨날 사용되는 LightGBM과 XGBoost 그에 대해서 알아보자

먼저 이 두가지 알고리즘은 Tree-based 알고리즘이라는 것을 알아두고 넘어가자

사실 우리는 가져다가 사용하기 때문에 개념보다는 파라미터가 뭐가 들어가고 뭐가 좋고 이런게 더 관심있을 것이다. 나는 Tabular 데이터를 다루는 능력은 미숙하지만 그래도 뭔지는 알고 쓰고 싶다! 그런데 사실 알아야할게 너무 많다. 추려보자

- Decision Tree

- Random Forest

- 앙상블의 유형
  + 보팅
  + 배깅
  + 부스팅
  + 스태킹

- LightGBM

- XGBoost

이 정도 알면.. 설명은 가능하겠지?

# Decision Tree

Decision Tree는 꽤 쉬울 것이다. 예를 들어 쉽게 물과 커피를 분류하는 Decision Tree 모델이 필요하다고 생각해보자

```
         (색이 있나요?)
               |
  -----[True]------[False]-----
  |                           |
(물)                        (커피)

```

너무 단순하게 했지만 물과 커피를 구분하는 것은 색깔일수도 있고 맛에 대한 정보일수도 있다.

무엇 일수도 ~ 이런게 특징이 된다.

(물)과 (커피) 그리고 (색이 있나요?)는 노드에 담겨있다.

(물)과 (커피)를 Leaf 노드라고 부르며 (색이 있나요?)는 Root 노드라고 불린다. (제일 위에 있으면 Root 노드)

이정도만 알고 Random Forest로 넘어가자

# Random Forest

직역하면 무작위의 숲인데 숲에는 나무가 많다. 보통 나무가 많은 곳이 숲이되는데 여기 Random Forest의 나무는 Decisin Tree가 된다.

좀 더 문제가 복잡해지면 Decision Tree 하나로는 과적합을 발생시키거나 좋은 성능을 가질 수 없을 것이다.

좀 더 일반화된 과적합이 없는 트리를 생성하고 싶지만 이는 매우 어렵다. 그러면 트리를 여러개 만들어보자

트리를 여러개 만들어서 앙상블을 하게 되면 더 일반화가 잘된 모델을 얻을 수 있을 것?? -> 이게 Random Forest

그러면 여기서 질문 .. 어떻게 앙상블 할 수 있을 것이고 어떻게 트리를 구성해야할까??

Forest를 구성하는 방법은 여러 특징을 Random으로 Sampling하여 구성한다. (**Random** Forest 니까 ㅎㅎ)

[이 곳](https://medium.com/@deepvalidation/title-3b0e263605de)에서 순서가 잘나와 있는 것 같습니다.

1. 30개의 주어진 요소 (predictor) 중 일부만 무작위로 선택합니다. 흡연 여부, 키, 몸무게, 나이가 선택되었다고 가정합니다.
4가지 요소들 중 건강 위험도를 가장 잘 예측하는 요소 한 가지를 고릅니다.

2. 만약 그 요소가 흡연 여부가 되었을 경우, 의사 결정 트리의 첫번째 단계가 생성됩니다.

3. 의사 결정 트리의 모든 단계를 1~2의 과정을 거쳐 생성합니다. 이렇게 한개의 트리가 생성되었습니다.

4. 3을 원하는 개수의 트리가 생성되기까지 반복합니다.

5. 트리의 개수는 데이터 사이언티스트가 원하는 만큼 생성이 가능합니다.
울창한 숲이 완성되었습니다. 숲에게 어느 한 사람에 대한 정보를 준다면, 나무들이 투표해서 한가지 의견으로 통합하여 결과를 알려줍니다.

# 앙상블

Random Forest는 bagging 방식으로 각자 데이터를 샘플링하고 그 후에 모든 분류기가 voting하는 방식으로 최종 결과를 예측한다. Random Forest는 이 Bagging에 속하는 알고리즘이라고 할 수 있다.

앙상블은 low variance, low bias를 가지는 모델을 얻고 싶을때 만든다. 하지만 bias와 variance는 trade-off 관계를 가진다.

- low bias: 각 모델이 데이터에 대해 (특정)정답을 잘 맞추기 때문에 정답과의 거리가 작아 bias가 낮다.
- high variance: 각 모델이 잘 맞추는 정답은 달라 variance가 높다.

목적: bias와 variance의 trade-off를 고려한 low variance, low bias를 얻어야한다.

- variance가 크고 bias가 작으면 overfitting
- variance가 작고 bias가 크면 underfitting

## 보팅(Voting)

- 하드 보팅: 다양한 모델에서 예측된 **결과**들의 개수로 최종 예측
- 소프트 보팅: 다양한 모델에서 예측된 **확률**들의 평균으로 최종 예측

각 모델에 전체 데이터 셋을 학습한다

## 배깅(Bagging)

모델마다 전체 데이터에서 샘플링을 통해 추출 된 샘플 데이터 셋으로 학습한다. 이때 샘플링 된 데이터를 bootstrap sample이라고 하며 내부적으로 중복 될 수 있다는 특징을 가진다.

## 부스팅(boosting)

약한 모델 여러 개 연결시켜서 강한 모델을 만드는 방법

- AdaBoost: 과소적합한 샘플(오답)의 가중치를 높여서 이전 모델을 보완한 새로운 모델을 만든다.
- Gradient Boosting: AdaBoost는 **오답 샘플**을 보완하는 대신 Gradient Boosting은 **오차**를 보완하도록 학습시킨다.
- 뒤에 설명할 LightGBM과 XGBoost는 여기에 속한다.

## 스태킹(stacking)

각 모델이 예측한 값을 다시 학습 데이터로 사용하는 방법입니다.

예측을 위한 모델이 3개있을 때 각자 학습해서 예측하면 3개의 예측 결과가 나오는데 그것들을 학습 데이터로 사용해 결과를 도출하는 모델을 학습하는 방법입니다.

*자세한 수식이나 구현은 다루지 않습니다. 구글에 다양한 자료가 많으니 참고하세요 ^_^*

---

# XGBoost(Extreme Gradient Boosting)

- Boosting보다 빠르게 만들기 위해 병렬 학습을 지원하는 XGBoost 라이브러리
- 성능이 좋다.

*추가예정*

---


# LightGBM

- 수평적인 Tree 확장 알고리즘이다.
- 작은 데이터에서 과적합이 쉽다.
- 큰 데이터에서 좋은 효과를 발휘한다.
- 빠르다.

*추가예정*

---

# LightGBM / XGBoost 파라미터

[이 곳](http://machinelearningkorea.com/2019/09/29/lightgbm-%ED%8C%8C%EB%9D%BC%EB%AF%B8%ED%84%B0/)에 잘 나와 있어서 참조하시면 될 것 같습니다.

- `max_depth / max_depth`: tree의 depth인데 이를 통해 overfitting과 underfitting을 조절

- `num_leaves / max_leaves`: 하나의 tree에서 가질 수 있는 최대 leaf의 수

- `bagging_fraction / subsample`: 행 샘플링

- `feature_fraction / colsample_bytree`: 열 샘플링

- `num_iterations / nrounds`: 반복 횟수(boosting의 횟수)

- `boosting / booster`: 부스팅 기법 선택
  + `LightGBM`: `gbdt`: gradient boosting tree, `rf`: random forest, dart: Dropout Regression Trees, `goss`: Gradient-based One-Side Sampling
  + `XGBoost`: `gblinear`, `gbtree`, `dart`

- `learning_rate / eta`: 학습률
