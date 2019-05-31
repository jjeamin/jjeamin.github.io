---
layout: post
title:  "Cosine Matric Learning"
summary: "Cosine Matric Learning 분석하기"
date:   2019-05-02 12:00 -0400
categories: paper
---

*논문의 번역과 이해가 내가 느끼기엔 어렵고 복잡하기 때문에 소스코드를 분석해서 이해해보는 것이 나을거 같다.*

- [PAPER](https://arxiv.org/abs/1812.00442)
- [GitHub](https://github.com/nwojke/cosine_metric_learning)
- [번역한 GitHub](https://github.com/aiaccel-lab/deep_sort)

---

# DataSet
MARS와 MARKET 2개가 있었지만 MARS는 다운로드가 잘안되서.. MARKET으로만 하겠습니다.

- [MARS](http://www.liangzheng.com.cn/Project/project_mars.html)
- [MARKET](https://jingdongwang2017.github.io/Projects/ReID/Datasets/Market-1501.html)

---

# 학습
깃허브의 소스코드를 `clone`하면 간단하게 실행을 시켜볼 수 있다. 이건 간략하게 부분만 가져온 것이고 자세하게 설치/실행하는 방법은 위에 깃허브의 `Read.md`를 읽어봐야한다.

```
python train_market1501.py \
    --dataset_dir=./Market-1501-v15.09.15/ \
    --loss_mode=cosine-softmax \
    --log_dir=./output/market1501/ \
    --run_id=cosine-softmax
```

---

# 개념
일단 코드를 살펴보기전에 `Euclidean`,`Cosine Distance`의 개념에 대해서 알고가야 매우 쉽게 할수 있다.

![distance](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/sort/distance.PNG)
원문 : [https://cmry.github.io/notes/euclidean-v-cosine](https://cmry.github.io/notes/euclidean-v-cosine)

## Euclidean
우리가 눈으로 보며 줄자를 이용해 거리를 측정하는 것과 유사하다.

```python
def _pdist(a, b):
    a, b = np.asarray(a), np.asarray(b)
    if len(a) == 0 or len(b) == 0:
        return np.zeros((len(a), len(b)))
    a2, b2 = np.square(a).sum(axis=1), np.square(b).sum(axis=1)
    r2 = -2. * np.dot(a, b.T) + a2[:, None] + b2[None, :]
    r2 = np.clip(r2, 0., float(np.inf))
    return r2

def _nn_euclidean_distance(x, y):
    distances = _pdist(x, y)
    return np.maximum(0.0, distances.min(axis=0))
```

## Cosine Distance
코사인 유사도는 무게와 크기는 고려하지않고 벡터사이의 각도만으로 측정한다.

```python
def _cosine_distance(a, b, data_is_normalized=False):
    if not data_is_normalized:
        a = np.asarray(a) / np.linalg.norm(a, axis=1, keepdims=True)
        b = np.asarray(b) / np.linalg.norm(b, axis=1, keepdims=True)
    return 1. - np.dot(a, b.T)
```

`deep sort`에서는 객체의 위치와 그 다음 프레임에서 객체의 위치를 찾기 위해 `cosine similarity`를 사용하는 것으로 추정된다. 엄청난 수식이지만 일단 풀어보진 않고 이렇다만 알아두고 넘어가야 할거 같다..

---

# 코드


# 참조
[http://docs.likejazz.com/cosine-sim/](http://docs.likejazz.com/cosine-sim/)
