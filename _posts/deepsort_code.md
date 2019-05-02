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

# DataSet
MARS와 MARKET 2개가 있었지만 MARKET은 url이 폐쇄된거 같기 때문에 MARS만 보겠습니다.

- [MARS](http://www.liangzheng.com.cn/Project/project_mars.html)


# 실행
깃허브의 소스코드를 `clone`하면 간단하게 실행을 시켜볼 수 있습니다. MARS 데이터셋을 사용하기 위해서는 변환이 필요한거 같습니다.


```
python train_mars.py \
    --dataset_dir=./MARS-evaluation-master \
    --loss_mode=cosine-softmax \
    --log_dir=./output/mars/ \
    --run_id=cosine-softmax
```
