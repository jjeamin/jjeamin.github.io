---
title:  "Pytorch lightning 사용하기"
summary: "Pytorch lightning 사용하기"
date:   2021-06-27 09:10 -0400
categories: pytorch
---

> 조금씩이라도 자주 써야겠다.

# Pytorch Lighting

항상 이런게 있구나 써봐야지 하면서 이제 써보는 Pytorch Lighting.. 진작 써볼껄.. 너무 편한거 같은 생김새와 실제로 사용할 때 매력적인 snippet들!


- [Github](https://github.com/PyTorchLightning/pytorch-lightning)


사실 public github만 보아도 사용법을 바로 습득할 수 있다. 한번 보고 넘어갑시다 ^^

먼저 사용하기 쉽다는 것은 무엇을 보고 알수 있을까?

- 하나의 클래스에서 모든 학습/추론
- multigpu distributed training
- 16-bit precision
- Metrics
- Logging
- Early Stopping
- Visualization
- ...

일단 Trick을 엄청 많이 사용할 수 있다. Competition 같은 곳에서 쓰이면 좋은 성능을 발휘할듯..?

1. 설치하기

```sh
pip install pytorch-lightning
```

2. 차근차근 시작하기
