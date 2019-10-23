---
layout: post
title:  "CornerNet"
summary: "CornerNet 논문 읽어보기"
date:   2019-10-22 13:00 -0400
categories: paper
use_math: true


---

# CornerNet

(CornerNet: Detecting Objects as Paired Keypoints)

- CornerNet Paper : [Here](https://arxiv.org/abs/1808.01244)
- Official Code : [Here](https://github.com/princeton-vl/CornerNet)

# Abstract

CornerNet은 bounding box를 왼쪽 위(top-left)와 오른쪽 아래(bottom-right)의 한 쌍의 keypoint로 감지하는 object detection에 대한 새로운 접근방법을 제안한다.

쌍을 이루는 keypoint로 object를 detection하기 때문에 anchor box를 만들 필요가 없다.

corner의 localize하는데 도움이 되는 새로운 유형의 corner pooling을 소개한다.

성능이 MS COCO에서 42.2% AP를 달성하여 기존 one-stage 검출기보다 성능이 뛰어나다.

# Introduce

SOTA의 기본적인 구성 요소는 다양한 크기와 종횡비를 가지는 anchor box다. one-stage detector는 이미지 위에 anchor box를 밀집시킨뒤 anchor box를 계산하고 box regression을 통해 coordinates를 수정해서 최종 bounding box를 만들어낸다.

하지만 anchor box를 사용하는데에는 2가지 단점이 있다.

- 매우 많은 anchor box가 필요하다.

각 anchor box가 ground truth box와 얼마나 겹치는지 여부를 분류하도록 검출기를 훈련시키기 때문에 ground truth box와 충분한 겹치는 anchor box를 찾기 위해 많은 anchor box가 필요하다. 결과적으로는 작은 부분의 anchor box가 겹치게 된다. 이는 positive anchor box(foreground)와 negative anchor box(background)사이에 큰 불균형을 유발하고 학습 속도를 늦춘다.

- 많은 hyper parameter가 생긴다.

anchor box의 개수, 크기, 종횡비가 hyper parameter이고 대체로 휴리스틱(사람의 선택)을 통해 이루어진다.


***이 논문에서는 새로운 접근 방식을 제안 : bounding box의 top-left 모서리와 botton-right의 모서리의 쌍을 예측하기***

single convolutional network를 사용해서 `top-left 모서리에 대한 heatmap`, `bottom-right 모서리에 대한 heatmap`, `한 쌍의 모서리를 그룹화 해줄 임베딩` 을 예측한다. 이러한 접근법은 human-pose estimation의 방식에서 영감을 얻었다.



![figure1](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/cornernet/figure1.PNG)



**그림1 : 전체 파이프 라인**





# REFERENCE
- PR12 : [https://www.youtube.com/watch?v=6OYmOtivQY8](https://www.youtube.com/watch?v=6OYmOtivQY8)
