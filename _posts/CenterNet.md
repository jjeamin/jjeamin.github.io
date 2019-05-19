---
layout: post
title:  "CenterNet"
summary: "CenterNet 논문 읽어보기"
date:   2019-05-19 13:00 -0400
categories: paper
---

초당 142 프레임에 28.1 `AP`를 가지고 있다고 해서 바로 읽어보았다.(GOOD)

- `AP` : recall 값들에 대응하는 precision 값들의 average

# CenterNet

- CenterNet Paper : [Here](https://arxiv.org/abs/1904.07850)(Object as Point)
- Github : [Here](https://github. com/xingyizhou/CenterNet)

---

# 요약
- Detection은 이미지의 `axis aligned box`로 object를 식별한다. 대부분의 성공적인 object detetor들은 잠재적 object 위치의 거의 모든 목록을 찾고 각각을 분류한다. 이러한 일은 낭비가 많고 비효율적고 추가적인 post-processing이 필요하다. **그래서 이 논문에서는 다른 접근법을 취한다.**

- 이 논문은 bounding box의 중심점인 단일 점으로 모델링한다. 이 논문의 detector는 `keypoint estimation`를 사용해서 중심점을 찾고 크기, 3D 위치, 방향, 자세와 같은 모든 다른 object의 속성으로 regression한다.

- MS COCO 데이터셋에
  + Resnet18 : `142FPS에서 28.1 AP`
  + DLA34 : `52FPS에서 37.4 AP`
  + Hourglass-104 : `1.4FPS에서 45.1 AP`

- [KITTI 벤치마크](http://www.cvlibs.net/datasets/kitti/eval_object.php?obj_benchmark=3d)에서 3D bounding box를 추정하고 [COCO keypoint 데이터셋](http://cocodataset.org/#download)에서 인간의 자세를 계산하기 위해 동일한 접근법을 사용한다.

---

# 소개
- 현재의 object detector는 object를 `axis-aligned bounding box`를 통해서 각 물체를 나타낸다. 그런 다음 object detection을 줄여 광범위한 잠재적 object bounding box를 분류한다.

- 1단계 검출기는 이미지 위에 anchor라고 하는 가능한 경계 상자의 복잡한 배치를 sliding하고 상자 내용을 지정하기 않고 직접 분류한다.

- 2단계 검출기는 잠재적 상자 각각에 대해 이미지의 특징을 다시 계산한 다음 특징을 분류한다.

- 이 후 처리는 구별하기 어렵고 훈련이 어렵다 따라서 대부분의 detector는 end-to-end 훈련이 불가능하다. 그런데도 좋은 성공을 거두었다.

- Slidingwindow 기반의 object detector는 가능한 모든 object의 위치를 찾아야하기 때문에 낭비가 된다. 그래서 그 대안이 이 논문이다.

- **object detection은 표준 key point exitmaion 문제이다.** 히트맵을 생성하는 convolution network에 이미지를 넣어주기만 하면 된다. 이 히트맵의 peaks는 물체 중심에 해당하고 각 peaks의 이미지 특징은 object bounding box의 높이 및 두께를 예측한다.

- 이 논문은 각 중심점에서 추가적인 출력을 예측하여 3D object detection 및 다중 사람 자세 추정에 대한 실험을 제공한다. 3차원 bounding box를 추정하기 위해서 object의 절대 깊이, 3D bounding box의 크기, object의 방향으로 회귀한다.



![그림1](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/centernet/그림1.PNG)



---

## Related work

### Object detection by region classification

가장 먼저 성공적인 object detectors 중 하나인 RCNN은 많은 region 후보들로 부터 대상 위치를 찾고 각각을 deep network를 사용하여 분류한다. Fast-RCNN은 계산을 저장하기 위해 이미지의 특징을 자른다. 그러나 두 방법 모두 저수준의 region proposal 방법에 의존한다.

### Object detection with implicit anchors

Faster RCNN은 detection network 내에서 region proposal을 생성한다. 저해상도 이미지 격자 주위에 `고정된 모양의 bounding box(anchor box)`를 sampling하고 각각을 foreground background로 분류한다. anchor는 모든 ground truth object에 0.7보다 크면 foreground로 표시되며 0.3보다 작으면 background로 표시되거나 무시된다. proposal classifier를 multi-class classification로 변경하는 것이 1단계 검출기의 기초를 형성한다. 1단계 검출기에 대한 몇 가지 개선사항으로는 `anchor shape priors`, `different feature resolution`, 다른 샘플 사이의 `loss re-weighting` 이 있다. 이 논문또한 anchor기반 1단계 검출기와 밀접하게 연관이 되어 있다.

- `anchor shape priors` : YOLOv2,YOLOv3
- `different feature resolution` : SSD
- `loss re-weighting` : Focal loss for dense object detection

그러나 몇가지 중요한 차이점이 있다.

1. CenterNet은 box의 겹침이 아닌 위치에 기반하여 "anchor"를 할당한다. foreground와 background에 대한 임계값이 없다.

2. object당 하나의 anchor만 있기에 NMS(NonMaximum Suppression)가 필요하지 않다. 단순히 keypoint 히트맵에서 peak를 추출한다.

3. CenterNet은 기존의 object detector과 비교해서 더 큰 출력 해상도를 사용한다. 이것이 다중 anchor의 필요성을 제거한다.

### Object detection by keypoint extimation

object detection을 위해서 keypoint 추정을 사용하는 것이 처음은 아니다. CornerNet은 두 개의 bounding box의 모서리를 keypoint로 탐지하는 반면 ExtremeNet은 모든 object의 상단,좌측,하단,우측 및 중심점을 탐지한다. 이 두가지 방법 모두 CenterNet과 동일한 keypoint 추정 네트워크를 기반으로 한다. 그러나 keypoint 추정후 그룹화 단계가 필요하고 그에 따라서 알고리즘 속도가 현저히 줄어든다. 반면 CenterNet은 그룹화 또는 후처리 작업 없이도 객체당 하나의 중심점만 추출한다.



![그림2](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/centernet/그림2.PNG)



---

## Preliminary

`I ∈ R^(W×H×3)`를 W인 폭과 H인 높이를 가진 이미지라고 해보면 이 논문의 목표는 keypoint 히트맵 `Y ∈ [0, 1]^(W/R × H/R × C)`를 생성하는 것이다. 여기서 R은 `output stride`이고 C는 keypoint 유형의 수다. keypoint의 유형은 인간의 자세의 추정에서 C=17(인간 관절), object detection에서 C=80 범주를 포함한다. 그리고 기본적으로 R=4이다. 예측값 `Y'(x,y,z) = 1`은 keypoint이고 `Y'(x,y,z) = 0`은 background이다. 이미지 I로부터 Y를 예측하기 위해 여러개의 fully convolution encoder-decoder network를 사용한다. 클래스 c의 각 groud truth인 keypoint `p ∈ R^2`에 대해 저해상도 `p' = [p/R]`를 계산한다. gaussian kernel인



![식1](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/centernet/식1.PNG)



를 사용하여 히트맵 `Y ∈ [0, 1]^(W/R × H/R × C)`에 모든 ground truth keypoint를 둔다. 여기서 σ(p)는 object size-adaptive 표준편차이다. 만약 동일한 클래스에 두 gaussian이 겹치면 요소 별 최대값을 취한다. 훈련의 목적은 focal loss이 있는 픽셀 단위의 logistic regression이다.



![식2](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/centernet/식2.PNG)



- `α,β` : focal loss의 hyper parameter
- `N` : 이미지 I의 keypoint의 수이다. N은 모든 양수의 focal los를 1로 정규화하기 위해 선택된다.
- Law and Deng을 따라서 `α` = 2, `β` = 4 로 정했다.
- output stride에 의해 발생된 수학적 오류를 복구하기 위해서 local offset을 각 중심점마다 추가로 예측한다. : `O' ∈ R^(W/R x H/R x 2)`

모든 클래스 c는 동일한 offset 예측을 공유한다. offset은 L1 loss로 훈련된다. 감독자는 오직 keypoint 위치 `p'`에서만 작동하고 다른 모든 위치는 무시된다. 다음 섹션에서 keypoint 추정을 범용 object detector로 확장하는 방법을 보여준다.

---

## Objects as Points




---

# 용어 정리

## axis aligned bounding box
- 줄여서 AABB라고도 불리는 축에 정렬된 경계 상자이다.

- 물체가 회전을 해도 경계 상자는 회전하지 않고 box의 최소점과 최대점을 가지고 충돌 비교를 한다.

- 2D가 아니고 3D의 개념이다.



![aabb](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/centernet/aabb.PNG)




## heatmap
- 열분포 형태의 지도

## outstride
우리가 기본적으로 필터가 얼마만큼 이동할지에 대해서 말할때 stride라고 하는데 그것은 input stride이고 output stride라는 말은 예를 들어 이미지가 224*224이고 마지막 특징맵이 7*7이면 output stride는 32이다. 즉, downsampling이 얼마나 되었는지에 대한 근사치이다.

---

# 참조
- [https://subscription.packtpub.com/book/game_development/9781787123663/7/ch07lvl1sec69/axis-aligned-bounding-box](https://subscription.packtpub.com/book/game_development/9781787123663/7/ch07lvl1sec69/axis-aligned-bounding-box)
