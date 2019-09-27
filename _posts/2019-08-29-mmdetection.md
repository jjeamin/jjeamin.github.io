---
layout: post
title:  "MMDetection"
summary: "MMDetection 논문 읽어보기 / 사용하기"
date:   2019-08-29 13:00 -0400
categories: paper
---

# MMDetection

(object detection tool box and benchmark)

- MMDetection Paper : [Here](https://arxiv.org/abs/1906.07155)
- Official code : [Here](https://github.com/open-mmlab/mmdetection)

object detection tool box인 MMDetection과 MMDetection이 지원하는 프레임워크들의 benchmark를 알아보자

# Frameworks

지원하는 프레임워크 KeyPoint

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



## Multi Stage

| Name | Content | Year |
| :------------ | :-----------: | -------------------: |
| `Casecade R-CNN` | `multi-stage` | 2017 |
| `Hybrid Task Cascade`  | `multi-stage` , `multi-branch` , `instance segmentation` | 2019 |



## General Modules and Methods


| Name | Content | Year |
| :------------ | :-----------: | -------------------: |
| `Mixed Precision Training` | `half precision ﬂoating point (FP16) ` | 2018 |
| `Soft NMS` | `new NMS` | 2017 |
| `OHEM` | `hard sampling` | 2016 |
| `DCN ` | `deformable convolution`, `deformable RoI pooling` | 2017 |
| `DCNv2` | `deformable operators` | 2018 |
| `ScratchDet` | `scratch`,`random initialization` | 2018 |
| `Train from Scratch` | `scratch` | 2018 |
| `M2Det` | `effective feature pyramids` | 2018 |
| `GCNet` | `global context block` | 2019 |
| `Generalized Attention` | `generalized attention formulation` | 2019 |
| `SyncBN`,`MegDet` | `batch normalization`, `synchronized ` | 2017 |
| `GroupNormalization` | `group batch normalization` | 2018 |
| `Weight Standardization` | `micro-batch training` | 2019 |
| `HRNet` | `high-resolution representations`, `backbone` | 2019 |
| `Guided Anchoring` | `new anchoring`, `sparse and arbitrary-shaped anchors` | 2019 |
| `Libra R-CNN` | `framework`, `balanced learning` | 2019 |



# Architecture

## Model Representations

- `Backbone` : fully connected layer가 없는 resnet-50

- `Neck` : feature map 수정/재구성 ex) FPN

- `DenseHead` : AnchorHead / AnchorFreeHead(`RPNHead`, `RetinaHead`, `FCOSHead`)를 포함하고 feature map의 밀집된 위치에서 작동한다.

- `RoIExtractor` : `RoIPooling`과 같은 연산을 사용해 `ROIwise feature`를 추출하는 부분이다. ex) SingleRoI

- `RoIHead ` : bounding box를 분류, 회귀, 마스크 예측



![figure1](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/mmdetection/figure1.PNG)



## Training Pipeline
- `hooking` :  함수 호출, 메시지, 이벤트 등을 중간에서 바꾸거나 가로채는 명령, 방법, 기술이나 행위를 말한다.

hooking mechanism을 가지고 있는 pipeline



![figure2](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/mmdetection/figure2.PNG)



# Benchmark

## Dataset
- VOC
- COCO



![figure3](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/mmdetection/figure3.PNG)



# 부록 : 사용하기

참조 : [깃허브](https://github.com/open-mmlab/mmdetection)

## 환경 설치하기

```
conda create -n open-mmlab python=3.7 -y
conda activate open-mmlab

conda install pytorch torchvision -c pytorch

git clone https://github.com/open-mmlab/mmdetection.git
cd mmdetection

pip install mmcv

python setup.py develop
# or "pip install -v -e ."

mkdir data
ln -s $COCO_ROOT data
```

## 데이터셋 준비하기

```
mmdetection
├── mmdet
├── tools
├── configs
├── data
│   ├── coco
│   │   ├── annotations
│   │   ├── train2017
│   │   ├── val2017
│   │   ├── test2017
│   ├── cityscapes
│   │   ├── annotations
│   │   ├── train
│   │   ├── val
│   ├── VOCdevkit
│   │   ├── VOC2007
│   │   ├── VOC2012
```

데이터셋을 다운로드하고 위와 같은 구조를 맞추어 주어야한다.

- [COCO](http://cocodataset.org/#download)
- [VOC2007](http://host.robots.ox.ac.uk/pascal/VOC/voc2007/)
- [VOC2012](http://host.robots.ox.ac.uk/pascal/VOC/voc2012/)
- [cityscapes](https://github.com/mcordts/cityscapesScripts)

```
cd data/cityscapes/
mv train/*/* train/
```

## 모델 준비하기

다운로드 : [https://github.com/open-mmlab/mmdetection/blob/master/docs/MODEL_ZOO.md](https://github.com/open-mmlab/mmdetection/blob/master/docs/MODEL_ZOO.md)

- 실행하기

*dataset demo*

```
python tools/test.py configs/faster_rcnn_r50_fpn_1x.py \
    checkpoints/faster_rcnn_r50_fpn_1x_20181010-3d1b3351.pth \
    --show
```

*bbox, mask AP*

```
python tools/test.py configs/mask_rcnn_r50_fpn_1x.py \
    checkpoints/mask_rcnn_r50_fpn_1x_20181010-069fa190.pth \
    --out results.pkl --eval bbox segm
```

*webcam demo*

```
python demo/webcam_demo.py configs/faster_rcnn_r50_fpn_1x.py \
    checkpoints/faster_rcnn_r50_fpn_1x_20181010-3d1b3351.pth
```

이것저것 테스팅을 해볼수 있는 유용한 toolbox다. [고성능 API](https://github.com/open-mmlab/mmdetection/blob/master/demo/inference_demo.ipynb)도 이용할 수 있기 때문에 사용이 편리하다.
