---
layout: post
title:  "MMDetection"
summary: "MMDetection 논문 읽어보기"
date:   2019-08-29 13:00 -0400
categories: paper
---

# MMDetection

(object detection tool box and benchmark)

- MMDetection Paper : [Here](https://arxiv.org/abs/1906.07155)
- Official code : [Here](https://github.com/open-mmlab/mmdetection)

object detection tool box인 MMDetection과 MMDetection이 지원하는 프레임워크들의 benchmark를 알아보자

# Frameworks

지원하는 프레임워크 한줄 설명

## Single stage

`input` -> `feature extraction` -> `detection`(`Localization`, `Classification `) -> `output`(`multi class classification`,`bounding box regression`)

Localization, Classification 을 동시에 해결

| Name | Content | Year |
| :------------ | :-----------: | -------------------: |
| `SSD` | `multi scale feature map` | 2015 |
| `RetinaNet` | `Focal loss` | 2017 |
| `GHM` | `gradient harmonizing mechanism` | 2019 |
| `FCOS` | `fully convolutional` ,**`anchor-free`** | 2019 |
| `FSAF` | `fully convolutional` ,**`anchor-free`** | 2019 |


## Two stage

`input` -> `region proposal`(`Localization`) -> `Classification ` -> `output`(`multi class classification`,`bounding box regression`)

Localization, Classification 을 순차적으로 해결

| Name | Content | Year |
| :------------ | :-----------: | -------------------: |
| `Fast R-CNN` | `Region Proposal(RP)` , `ROI Pooling` | 2015 |
| `Faster R-CNN` | `Region Proposal Network(RPN)`,`Fast R-CNN` | 2015 |
| `R-FCN` | `fully convolutional`, `Faster R-CNN` | 2016 |
| `Mask R-CNN` | `Binary Mask` ,`RoI Align` , `Faster RCNN` | 2017 |
| `Grid R-CNN` | `grid guided localization mechanism(bounding box regression imporved)`, `RPN` | 2018 |
| `Mask Scoring R-CNN` | `mask IoU prediction`, `Mask R-CNN` | 2019 |
| `Double-Head R-CNN` | `convolution head(localization) + fully connected head(classification)` | 2019 |

## General Modules and Methods
