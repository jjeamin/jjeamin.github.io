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
MARS와 MARKET 2개가 있었지만 MARS는 다운로드가 잘안되서.. MARKET으로만 하겠습니다.

- [MARS](http://www.liangzheng.com.cn/Project/project_mars.html)
- [MARKET](https://jingdongwang2017.github.io/Projects/ReID/Datasets/Market-1501.html)

# 학습
깃허브의 소스코드를 `clone`하면 간단하게 실행을 시켜볼 수 있다.

```
python train_market1501.py \
    --dataset_dir=./Market-1501-v15.09.15/ \
    --loss_mode=cosine-softmax \
    --log_dir=./output/market1501/ \
    --run_id=cosine-softmax
```
