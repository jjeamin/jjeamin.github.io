---
layout: post
title:  "Darknet 완전분석하기"
summary: "Darknet 완전분석하기"
date:   2019-04-20 12:00 -0400
categories: Darknet
---

# /cfg
- 모델 구성이 들어있는 디렉토리

# /data
- 9k.label : YOLO9000 dataset label
- 9k.names : YOLO9000 dataset names
- 9k.tree : YOLO9000 tree 정보(label:tree번호)
- coco.names : coco dataset names(80개)
- coco9k.map : coco를 tree에 결합한 정보(tree번호)
- imagenet.label.list : imagenet dataset label
- imagenet.shortnames.list : imagenet dataset names
- voc.names : voc dataset names
- openimages.names : openimages dataset names
- inet9k.map : imagenet을 tree에 결합한 정보

## /data/labels
- 알파벳 이미지가 크기별로 들어있는 폴더로 label을 보여줄때 사용하는 것 같다
