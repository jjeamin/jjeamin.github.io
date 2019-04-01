---
layout: post
title:  "YOLOv2"
summary: "YOLOv2 논문 읽어보기"
date:   2019-03-23 13:00 -0400
categories: yolo
---

# YOLOv2

- YOLO 9000 Paper : [Here](https://arxiv.org/abs/1612.08242)

---
# YOLO가 빠른 이유
- bounding box의 위치를 찾는 것과 classification이 동시에 이루어 지기 때문에 속도가 빠르다. 반면에 R-CNN 계열의 검출 네트워크들은 영상에서 오브젝트가 있을 것 같은 후보(ROI - Region Of Interest : 관심영역) 를 먼저 뽑는다. 후보로 뽑힌 ROI(영상의 작은 부분) 들은 Classification network(분류기)에 의해 클래스 분류가 이뤄지고  그 후 bounding box 를 찾는다.


# YOLOv2

# 요약
9000가지 이상의 object detection을 수행할 수 있는 YOLOv2를 소개한다. PASCAL VOC와 COCO와 같은 표준 detection 작업에 대한 기술이다. 혁신적 multi-scale train 방법을 사용하면 속도와 정확도의 균형을 잘 유지 할 수 있다. 67FPS에서 YOLOv2는 76.8 mAP을 얻었고 40FPS에서는 78.6 mAP를 얻었다. 마지막으로 YOLO는 object detection과 classification에 대해서 jointly train(공동 훈련) 방법을 제안한다. 이 방법을 사용하여 YOLO는 9000 개 이상의 서로 다른 object의 class를 예측한다. 그리고 그것은 여전히 **실시간** 으로 진행된다.

# 소개(introduce)
일반적인 object detector은 빠르고,정확하고,다양한 물체를 인식할 수 있어야한다. Neural Net의 도입 이후 프레임 워크는 점점 더 빠르고 정확해졌다. 그러나 대부분의 detection 방법은 여전히 작은 object 집합에 제약을 받는다.

# 참조
- ssd
  + [https://m.blog.naver.com/sogangori/221007697796](https://m.blog.naver.com/sogangori/221007697796)

- yolo
  + [https://m.blog.naver.com/sogangori/220993971883](https://m.blog.naver.com/sogangori/220993971883)
  + [https://m.blog.naver.com/sogangori/221011203855](https://m.blog.naver.com/sogangori/221011203855)
