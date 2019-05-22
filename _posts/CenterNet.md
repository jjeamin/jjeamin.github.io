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

- 이 논문은 bounding box의 중심점인 단일 점으로 모델링한다. 이 논문의 detector는 `keypoint estimation`를 사용해서 중심점을 찾고 크기, 3D 위치, 방향, 자세와 같은 모든 다른 object의 속성으로 회귀한다.

- MS COCO 데이터셋에
  + Resnet18 : `142FPS에서 28.1 AP`
  + DLA34 : `52FPS에서 37.4 AP`
  + Hourglass-104 : `1.4FPS에서 45.1 AP`

- [KITTI 벤치마크](http://www.cvlibs.net/datasets/kitti/eval_object.php?obj_benchmark=3d)에서 3D bounding box를 추정하고 [COCO keypoint 데이터셋](http://cocodataset.org/#download)에서 인간의 자세를 계산하기 위해 동일한 접근법을 사용한다.

---

# 소개
- 현재의 object detector는 object를 `axis-aligned bounding box`를 통해서 각 물체를 나타낸다. 그런 다음 object detection을 줄여 광범위한 잠재적 object bounding box를 분류한다.

- 1단계 검출기는 이미지 위에 anchor라고 하는 가능한 bounding box의 복잡한 배열을 sliding하고 box의 내용을 지정하지 않고 직접 분류한다.

- 2단계 검출기는 잠재적 상자 각각에 대해 이미지의 특징을 다시 계산한 다음 특징을 분류한다.

- 후처리를 할 때 구별하기 어렵고 훈련이 어렵다. 따라서 대부분의 detector는 end-to-end 훈련이 불가능하다. 그런데도 좋은 성공을 거두었다.

- Sliding window 기반의 object detector는 가능한 모든 object의 위치를 찾아야하기 때문에 낭비가 된다. 그래서 그에 따른 대안을 이 논문이 설명한다.

- **object detection은 표준 key point estimaion 문제이다.** 히트맵을 생성하는 convolution network에 이미지를 넣어주기만 하면 된다. **이 히트맵의 peaks는 물체 중심에 해당하고 각 peaks의 image feature는 object bounding box의 높이 및 두께를 예측한다.**

- 이 논문은 각 중심점에서 추가적인 출력을 예측하여 3D object detection 및 다중 사람 자세 추정에 대한 실험을 제공한다. 3차원 bounding box를 추정하기 위해서 object의 절대 깊이, 3D bounding box의 크기, object의 방향을 회귀한다.



![그림1](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/centernet/그림1.PNG)



---

# Related work

## Object detection by region classification

가장 먼저 성공적인 object detectors 중 하나인 RCNN은 많은 region 후보들로 부터 대상 위치를 찾고 각각을 deep network를 사용하여 분류한다. Fast-RCNN은 계산을 저장하기 위해 이미지의 특징을 자른다. 그러나 두 방법 모두 성능이 좋지않은 region proposal 방법에 의존한다.

## Object detection with implicit anchors

Faster RCNN은 detection network 내에서 region proposal을 생성한다. 저해상도 이미지 격자 주위에 `고정된 모양의 bounding box(anchor box)`를 sampling하고 각각을 foreground background로 분류한다. anchor는 모든 ground truth object에 0.7보다 크면 foreground로 표시되며 0.3보다 작으면 background로 표시되거나 무시된다. proposal classifier를 multi-class classification로 변경하는 것이 1단계 검출기의 기초를 형성한다. 1단계 검출기에 대한 몇 가지 개선사항으로는 `anchor shape priors`, `different feature resolution`, 다른 샘플 사이의 `loss re-weighting` 이 있다. **이 논문 또한 anchor기반 1단계 검출기와 밀접하게 연관이 되어 있다.**

- `anchor shape priors` : YOLOv2,YOLOv3
- `different feature resolution` : SSD
- `loss re-weighting` : Focal loss for dense object detection

그러나 몇가지 중요한 차이점이 있다.

1. CenterNet은 box의 겹침이 아닌 위치에 기반하여 "anchor"를 할당한다. foreground와 background에 대한 임계값이 없다.

2. object당 하나의 anchor만 있기에 NMS(NonMaximum Suppression)가 필요하지 않다. 단순히 keypoint 히트맵에서 peak를 추출한다.

3. CenterNet은 기존의 object detector과 비교해서 더 큰 출력 해상도를 사용한다. 이것이 다중 anchor의 필요성을 제거한다.

## Object detection by keypoint extimation

object detection을 위해서 keypoint 추정을 사용하는 것이 처음은 아니다. CornerNet은 두 개의 bounding box의 모서리를 keypoint로 detection하는 반면 ExtremeNet은 모든 object의 상단,좌측,하단,우측 및 중심점을 detection한다. 이 두가지 방법 모두 CenterNet과 동일한 keypoint 추정 네트워크를 기반으로 한다. 그러나 keypoint 추정후 그룹화 단계가 필요하고 그에 따라서 알고리즘 속도가 현저히 줄어든다. 반면 CenterNet은 그룹화 또는 후처리 작업 없이도 object당 하나의 중심점만 추출한다.



![그림2](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/centernet/그림2.PNG)



---

# Preliminary

`I ∈ R^(W×H×3)`를 W인 폭과 H인 높이를 가진 이미지라고 해보면 이 논문의 목표는 keypoint 히트맵 `Y ∈ [0, 1]^(W/R × H/R × C)`를 생성하는 것이다. 여기서 R은 `output stride`이고 C는 keypoint 유형의 수다. keypoint의 유형은 인간의 자세의 추정에서 C=17(인간 관절), object detection에서 C=80 범주를 포함한다. 그리고 기본적으로 R=4이다. 예측값 `Y'(x,y,z) = 1`은 keypoint이고 `Y'(x,y,z) = 0`은 background이다. 이미지 I로부터 Y를 예측하기 위해 여러개의 fully convolution encoder-decoder network를 사용한다. 클래스 c의 각 groud truth인 keypoint `p ∈ R^2`에 대해 저해상도 `p' = [p/R]`를 계산한다. gaussian kernel인



![식1](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/centernet/식1.PNG)



를 사용하여 히트맵 `Y ∈ [0, 1]^(W/R × H/R × C)`에 모든 ground truth keypoint를 둔다. 여기서 σ(p)는 object size-adaptive 표준편차이다. 만약 동일한 클래스에 두 gaussian이 겹치면 요소 별 최대값을 취한다. 훈련의 목적은 focal loss가 있는 픽셀 단위의 logistic regression이다.



![식2](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/centernet/식2.PNG)



- `α,β` : focal loss의 hyper parameter
- `N` : 이미지 I의 keypoint의 수이다. N은 모든 양수의 focal los를 1로 정규화하기 위해 선택된다.
- Law and Deng을 따라서 `α` = 2, `β` = 4 로 정했다.
- output stride에 의해 발생된 수학적 오류를 복구하기 위해서 local offset을 각 중심점마다 추가로 예측한다. : `O' ∈ R^(W/R x H/R x 2)`

모든 클래스 c는 동일한 offset 예측을 공유한다. offset은 L1 loss로 훈련된다. 감독자는 오직 keypoint 위치 `p'`에서만 작동하고 다른 모든 위치는 무시된다. 다음 섹션에서 keypoint 추정을 범용 object detector로 확장하는 방법을 보여준다.



![식3](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/centernet/식3.PNG)



---

## Objects as Points

범주 C(k)를 갖는 대상 k의 bounding box가 `x1`,`y1`,`x2`,`y2` 라고 하자. 중심점은 `p(k) = ((x1+x2)/2,(y1+y2)/2)`에 놓여있다. 예측 keypoint `Y'`을 사용해서 모든 중심점을 예측한다. 또한 각 object k에 대해 `s(k) = ((x2-x1),(y2-y1))`로 회귀한다. 계산적인 부담을 줄이기 위해 모든 object 범주에 대해 단일 크기 예측 `S' ∈ R^(W/R x H/R x 2)`을 사용한다. 그리고 목표2와 비슷한 중심점에 L1 loss를 사용한다.



![식4](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/centernet/식4.PNG)



scale을 표준화하지 않고 원시 픽셀 좌표를 직접 사용한다. 대신 loss를 일정한 λsize로 조정한다. 전반적인 교육 목표는



![식5](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/centernet/식5.PNG)



논문에서 달리 명시하지 않는이상 모든 실험에서 `λsize = 0.1` 와 `λoff = 1`로 설정했다. 단일 네트워크를 사용하여 keypoint `Y'`, offset `O'`, 크기 `S'`를 예측한다. 네트워크는 각 위치에서 총 `C+4`개의 output을 예측한다. 모든 출력은 `commonfully-convolutional backbone network`를 공유한다. 각 형태에 대해서 backbone의 특징은 별도의 3x3 convolution, relu와 다른 1x1 convolution을 통과한다.

### From points to bounding boxes

추론을 할때, 먼저 각 범주에 대한 히트맵의 peaks를 추출한다. 값이 8개의 연결된 이웃들 보다 크거나 같은 모든 응답을 detection하고 최고 100개의 peaks를 유지한다. `P'(c)`는 클래스 c의 n개의 검출된 중심점 `P' = {(xi'yi')}(i = 1~n)` 의 집합이라고 하자. 각 keypoint의 위치는 정수 좌표 (xi,yi)로 표시된다. keypoint 값 `Y'(x,y,z)`를 검출 신뢰도와 동일하게 사용하고 위치에 bounding box를 생성한다.



![그림3](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/centernet/그림3.PNG)



여기서 `(δ'(xi),δ'(yi)) = O'(xi',yi')`는 offset 예측이고, `(wi',hi') = S'(xi',yi')`는 사이즈 예측이다. 모든 출력은 IoU기반 NMS(Non Maximum Suppression) 또는 기타 후처리가 필요없는 keypoint 추정에서 직접 생성된다. peak keypoint 추출은 충분한 NMS 대안으로서 역할을 하고 3 x 3 max pooling 연산을 사용하는 장치에서 효율적으로 구현 될수 있다.

## 3D detection

- 3D detection은 object 당 3D bounding box를 추정하고 중심점 당 3개의 추가 속성(깊이,3D면적 및 방향)을 필요로 한다. 각각에 대해 별도의 분리된 head를 추가한다.

- 깊이 d는 중심점 당 하나의 스칼라이다. 그러나 깊이는 직접 회귀하기가 어렵다. 대신 `Eigen et al.`의 출력 변환을 사용한다. 그리고 `d = 1/σ(d') - 1` 여기서 σ는 sigmoid 함수이다. keypoint 추정기의 추가 출력 채널` D' ∈ [0,1]^(W/R x H/R)`로서 깊이를 계산한다.

- Relu로 분리된 2개의 convolution layer를 다시 사용한다. 이전 방식과 달리 출력에서 역 S자형 변환(inverse sigmoidal transformation)을 사용한다. S자형 변환 후 원래 깊이 에서 L1 loss를 사용해 깊이 추정기를 훈련시킨다.

- object의 3D 면적은 3개의 스칼라값이다. 별도의 `Γ' ∈ R^(W/R x H/R x 3)` 과 L1 loss를 사용해서 미터값의 절대값으로 직접 회귀한다. 방향은 기본적으로 단일 스칼라이지만 회귀가 어렵다. `Mousavian et al.`,`in-bin regression` 와 함께 두개의 `bins`로 방향을 표현한다. 특히 방향은 8개의 스칼라와 4개의 스칼라를 사용하여 인코딩한다. 하나의 bin에 대해 softmax 분류에는 두개의 스칼라가 사용되고 나머지 두개의 스칼라는 각 bin내의 각도로 회귀한다.

## Human pose estimation

사람의 자세를 추정하는 것은 모든 인간에 대해 k개의 2D 인간 관절 위치를 추정하는 것을 목표로 한다 (COCO k = 17). 포즈를 중심점의 k x 2 차원 속성으로 간주하고 각 keypoint를 중심점에 대한 간격을 띄우는 방식으로 매개변수화 한다. 직접적으로 L1 loss를 갖는 픽셀 단위의 관절 offset `J' ∈ R^(W/R x H/R x k x 2)`로 회귀한다. 그리고 보이지 않는 keypoint는 무시한다. 결과적으로 `slow-RCNN`과 유사한 회귀 기반의 1단계 사람의 자세 추정기가 된다. keypoint를 다시 정의하기 위해서, 우리는 `standard bottom-up multi-human pose extimation`을 사용해서 k 개의 사람의 관절 히트맵 `Φ' ∈ R^(W/R × H/R × k)` 을 추정한다. 중심 검출과 유사한 `focal loss`와 `local pixel offset`을 가진 사람의 관절 히트맵을 훈련한다.

그러면 이 히트맵에서 가장 가깝게 검출된 keypoint에 초기 예측을 스냅한다. 여기서 중심 오프셋은 가장 가까운 사람에 개별 keypoint 탐지를 할당하는 그룹 큐 역할을 한다. 구체적으로 (x',y')가 검출된 중심점이라고 하자. `j= 1 ~ k`에 대한 모든 접합 위치 `l(j) = (x',y') + J'(x',y',j')`로 회귀한다. 또한 대응하는 히트맵 `Φ(..j)`로 부터 각 관절 유형에 대해 `confidence > 0.1`인 모든 keypoint 위치 `L(j) = {l'(ji)} (i = 1 ~ n(j))` 다음으로 회귀된 각 위치 lj를 검출된 물체의 바운딩 박스 내에서 관절 검출만을 고려하여 가장 가깝게 검출된



![식6](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/centernet/식6.PNG)



에 할당된다.

## Implementation details

이 논문은 `ResNet-18`, `ResNet101`, `DLA-34`, `Hourglass-104`의 4 가지 아키텍처를 실험한다. `deformable convolution layer`를 사용하여 ResNet과 DLA-34를 수정하고 Hourglass network를 그대로 사용한다.

### Hourglass

`Hourglass Network`는 input을 4배씩 downsampling하고 순차적으로 두개의 Hourglass modules을 downsampling한다. 각 Hourglass modules는 skip connection을 사용하는 대칭 `5 layer down- and up-convolution network`다. 이 네트워크는 꽤 큰 모델이지만 일반적으로 최상의 keypoint 추정 성능을 가지고 있다.

### ResNet

고해상도 출력을 하기 위해서 3개의  `up-convolution network`를 사용하여 `residual network`을 강화했다. 계산을 저장하기 위해 먼저 3개의 upsampling layer의 채널을 각각 256,128,64로 변경한다. 그런 다음 각각 up-convolution 전에 3x3 `deformable convolution layer`를 추가한다. up-convolution kernel은 선형 보간으로 초기화된다.

### DLA

DLA(Deep Layer Aggregation)는 `hierarchical skip connection`을 사용하는 이미지 분류 네트워크다. 밀도 예측을 위해 DLA의 fully convolution upsampling 버전을 사용하며 feature map의 해상도를 높이기 위해 반복적으로 `deep aggregation`을 사용한다. low layer 에서 output까지 `deformable convolution layer`를 사용해서 `skip connection`을 강화한다. 구체적으로 원래 convolution을 모든 upsampling layer에서 3x3 `deformable convolution layer`으로 바꾼다.

### Training

- `input` : 512x512 / `output` : 128x128
- `data argument` : random flip, random scaling, cropping, color jitering
- `optimize` : Adam

cropping, scaling이 3D 측정값을 변경시키기 때문에 argumentation을 사용하지 않는다.

**`residual network`와 `DLA-34`**

 - `batch size`가 128(8 GPU)이고 `learning rate`가 140 epoch 동안 5e-4 이며 90과 120 epoch에서는 각각 10배씩 줄인다.

 - downsampling layer는 image Net의 pretrain으로 초기화되고 upsampling layer는 무작위로 초기화 된다.


**`Hourglass-104`**

`ExtremeNet`을 따르고 배치 크기를 29(5 GPU), 40 epoch에서 10배 줄이고 50 epoch에서 학습 속도 2.5e-4를 사용한다. detection 하기 위해 `ExtremeNet`에서 `Hourglass-104`를 계산해 계산량을 절약한다.

### Inference

- 3 가지 등급의 test argumentation을 사용한다 : `no argumentation`, `flip argumentation`, `multi scale(0.5,0.75,1.25,1.5)`

- flip의 경우 bounding box를 decoding 하기 전에 네트워크의 output의 평균을 구한다.

- multi scale의 경우 NMS를 사용하여 결과를 합친다.

## Experiments



---

# 용어 정리

## axis aligned bounding box
- 줄여서 AABB라고도 불리는 축에 정렬된 경계 상자이다.

- 물체가 회전을 해도 경계 상자는 회전하지 않고 box의 최소점과 최대점을 가지고 충돌 비교를 한다.

- 2D가 아니고 3D의 개념이다.

## NMS
후처리 단계이다. 그것은 object detection 에서 각각의 검출된 object에 대해 하나의 bounding box에 있는 많은 부정확한 물체 window hypotheses를 유발하는 smooth response map을 변환하는데 사용한다. 이상적으로 각 object에 하나의 bounding box가 있어야한다.

## heatmap
- 열분포 형태의 지도

## outstride
우리가 기본적으로 필터가 얼마만큼 이동할지에 대해서 말할때 stride라고 하는데 그것은 input stride이고 output stride라는 말은 예를 들어 이미지가 224 * 224이고 마지막 특징맵이 7 * 7이면 output stride는 32이다. 즉, downsampling이 얼마나 되었는지에 대한 근사치이다.

## backbone
backbone이란 등뼈라는 뜻을 가지고 있다. 등뼈는 뇌와 몸의 각 부위의 신경을 이어주는 역할을 한다. backbone은 입력이 처음 들어와서 출력에 관련된 모듈에 처리된 입력을 보내주는 역할이라고 생각할 수 있다. 결국 object를 검출하거나 영역을 나누던가 신경망 네트워크는 입력 이미지로 부터 다양한 feature를 추출해야하고 그 역할을 backbone이 한다.

## L1 loss
- `Least Absolute Deviations`
- L = |실제값 - 예측값|의 1~n까지의 합

## bin
히스토그램의 한 cell

## Hourglass Network

## ResNet

## 선형 보간

## DLA

deformable convolution layer
hierarchical skip connection
deep aggregation

## color jitering
이미지의 채도를 랜덤하게 노이즈를 주는 data argumentation 방식 중 하나

---

# 참조
- [https://subscription.packtpub.com/book/game_development/9781787123663/7/ch07lvl1sec69/axis-aligned-bounding-box](https://subscription.packtpub.com/book/game_development/9781787123663/7/ch07lvl1sec69/axis-aligned-bounding-box)
