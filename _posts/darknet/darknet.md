---
layout: post
title:  "Darknet 프로젝트 완전분석하기"
summary: "Darknet 프로젝트 완전분석하기"
date:   2019-04-25 12:00 -0400
categories: Darknet
---
> 이 문서는 darknet 프로젝트를 모두 분석하는 문서입니다.

# c언어 정리
- 프로젝트 : 하나의 실행파일을 만들어 내기 위해 필요한 여러 개의 소스 파일과 헤더 파일 등을 하나로 묶어 놓은 것을 말한다.
- `프로젝트이름.vcxproj` : 프로젝트,실행파일이나 dll(동적 연결 라이브러리)등과 같은 파일을 만든다.
- 솔루션 : 여러 개의 프로젝트의 모임, 응용 프로그램마다 하나가 존재하고 프로젝트를 생성하는 과정에서 프로젝트와 동일한 이름으로 자동 생성된다.
- `솔루션이름.sln` : 솔루션,프로젝트를 관리

---

# 순서
1. 데이터가 어떤 형식으로 저장이 되어있는지 Data Folder 확인
2. 예제 파일을 보면서 모든 함수 분석
3. cfg를 보고 모델을 어떻게 구성했는지 분석

---



![data](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/darknet/data.PNG)



# /data

- `9k.label` : YOLO9000 dataset label
- `9k.names` : YOLO9000 dataset names
- `9k.tree` : YOLO9000 tree 정보(label:tree번호)
- `coco.names` : coco dataset names(80개)
- `coco9k.map` : coco를 tree에 결합한 정보(tree번호)
- `imagenet.label.list` : imagenet dataset label
- `imagenet.shortnames.list` : imagenet dataset names
- `voc.names` : voc dataset names
- `openimages.names` : openimages dataset names
- `inet9k.map` : imagenet을 tree에 결합한 정보

## Imagenet
Imagenet은 어떠한 사진을 보여주었을 때 이 사진이 무엇인지 맞출 수 있는 컴퓨터를 만드는 대규모 프로젝트다.

## YOLO9000

- [논문 정리](https://jjeamin.github.io/paper/2019/04/20/yolo_v2/)

## PASCAL Visual Object Classes
PASCAL VOC는 2005 ~ 2012년에 진행이된 컴퓨터 비전 분야에서 object class recognition 기술을 겨루는 국제대회다.

클래스는 `사람,새,고양이,소,개,말,양,비행기,자전거,보트,버스,승용차,오토바이,기차,병,의자,식탁,화초,소파,모니터` 가 있다.

## COCO Dataset
- [홈페이지](http://cocodataset.org/#home)
- [API](http://cocodataset.org/#download)

object detection,segmentation,keypoint detection 등을 위한 dataset으로 여러 대회에 사용이 되고 있다. 특징은 아래와 같다. segmentation이 아래에 사진처럼 처리가 되어있다.

- `Object segmentation`
- `Recognition in context`
- `Superpixel stuff segmentation`
- `330K images` (>200K labeled)
- `1.5 million object instances`
- `80 object categories`
- `91 stuff categories`
- `5 captions per image`
- `250,000 people with keypoints`



![coco](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/darknet/coco.PNG)



---

# /data/labels
- 알파벳 이미지가 다양한 크기별로 들어있는 폴더로 name을 이미지로 나타낼 때 사용된다고 생각 할 수 있다.

*프로젝트를 분석하는 방법을 제대로 숙지해서 살펴보자*

---

# doxygen

코드를 문서화 해주는 프로그램으로 주석을 달면 문서로 정리가 되서 나오기 때문에 시각적으로 보기 편하다. darknet에 많은 주석을 달지는 않을 것 같지만 구조를 파악하는데 보기 쉽다.

```
sudo apt-get install doxygen
sudo apt-get install doxygen-gui
sudo apt-get install graphviz

doxywizard
```

---

yolo의 방식은 classification과 bounding box를 동시에 찾는것이고 미리 학습된 모델을 사용하는 것이 특징이다. end to end가 되지않기에 후처리가 필요하며 그에 반해서 성능이 괜찮다. 검출시 중간마다 3번의 detection을 하고 그 3개의 값을 전부 이용해서 x,y,w,h,confidence,class를 예측한다.

`output` : (n_anchor x (n_class + 5) ,13 ,13)
`post processing` : (13 x 13 x n_anchor ,n_class + 5)
