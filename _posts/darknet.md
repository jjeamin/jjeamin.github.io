---
layout: post
title:  "Darknet 완전분석하기"
summary: "Darknet 완전분석하기"
date:   2019-04-20 12:00 -0400
categories: Darknet
---
# c언어
- 프로젝트 : 하나의 실행파일을 만들어 내기 위해 필요한 여러 개의 소스 파일과 헤더 파일 등을 하나로 묶어 놓은 것을 말한다.
- `프로젝트이름.vcxproj` : 프로젝트,실행파일이나 dll등과 같은 파일을 만든다.
- 솔루션 : 여러 개의 프로젝트의 모임, 응용 프로그램마다 하나가 존재하고 프로젝트를 생성하는 과정에서 프로젝트와 동일한 이름으로 자동 생성된다.
- `솔루션이름.sln` : 솔루션,프로젝트를 관리


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
