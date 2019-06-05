---
layout: post
title:  "YOLO People counting"
summary: "라즈베리파이에서 NCS2를 이용해 yolo people counting"
date:   2019-03-28 13:00 -0400
categories: yolo
---

# YOLO
`You Only Look Once: Unified, Real-Time Object Detection`

- [YOLO](https://jjeamin.github.io/paper/2019/03/23/yolo/) <- 논문 번역

YOLO를 이용하여 `people counting`을 해보려고 합니다. 일단 `people counting`하기 전에 yolo를 이용해 car를 counting하는 소스 부터 찾아서 분석해 보겠습니다.

# YOLO Traffic Counter

[[깃허브](https://github.com/guillelopez/python-traffic-counter-with-yolo-and-sort)]

이미 학습되어있는 YOLO를 이용하고 opencv를 이용해서 차량을 검출하고 counting 해주는 소스입니다.

---

# Dependency
- python 3.7

# requirement
- opencv
- numba
- sklearn
- filterpy
- imutils

```
pip install opencv-python
pip install opencv-contrib-python
pip install imutils numba filterpy sklearn
```

# 실행

- weight 파일을 yolo-coco 폴더에 넣는다.

- git 가져오기

```
git clone https://github.com/guillelopez/python-traffic-counter-with-yolo-and-sort.git
```

- 실행

```
python main.py --input input/highway.mp4 --output output/highway.avi --yolo yolo-coco
```

시간이 조금 걸린다. output 폴더를 보면 프레임이 전부 사진으로 저장되고 영상이 하나 나오는데 영상을 보면 잘 counting 하는 것을 볼 수 있다. 뒤에 부분에 큰 트럭이 화면밖으로 나가면서 counting을 못했지만 살짝 수정하면 가능할 것 같습니다.

---

# 수정
- 코드 주석 한글화
- 이미지 저장하는것 해제
- tqdm 추가로 변환 상태바 추가

[[깃허브](https://github.com/jjeamin/python-traffic-counter-with-yolo-and-sort)]

# 참고해서 직접 코딩할 것
- 중심점으로 추적선을 만들지 않고 그보다 조금더 앞에 만든다. (중간 쯤에 트럭을 검출 못함)
- 사람만 카운팅하게 할 것
- 동영상말고 캠으로 동작하게 할 것
- 라즈베리파이에서..

---

# YOLO People Counting

[[깃허브](https://github.com/jjeamin/People_counting_yolo)]

# 구성
- raspberry pi 3 (rasbian)
- webcam
- NCS2

# requirement
- openvino toolkit [[설치방법](https://jjeamin.github.io/pi/2019/03/08/NCS2/)]
- opencv
- numba
- sklearn
- filterpy
- imutils

---

## SORT requirement 설치 (numba 설치가 안될 때 보세요)

```
sudo apt install libblas-dev llvm python3-pip python3-scipy
pip3 install llvmlite==0.15.0
pip3 install numba==0.30.1
```

중간에 numba 0.30.1을 설치할 때 계속 llvmlite 0.28.0을 설치하려는 경향 때문에 오류가 계속 발생했다. 만약 이런오류가 발생한다면 [여기](https://pypi.org/project/numba/0.30.1/) 에 들어가서 받고

```
cd numba-0.30.1
python setup.py build_ext --inplace
python setup.py install
```

- 중간에 pip을 덮어서.. pip3가 망가짐 그래서 아래와 같이 사용

```
python3 -m pip ~
```

- 남은 requirement 설치

```
python3 -m pip install sklearn tqdm imutils
```

---

# source
- [깃허브](https://github.com/jjeamin/People_counting_yolo)

### /test
- camera test
- camera thread

### /extension
- ir에 필요한 json 파일

### /pd_convert
- ir에 필요한 pd파일 변환 소스

- yolov3
  ```
  python convert_weights_pb.py
  --class_names coco.names
  --data_format NHWC
  --weights_file yolov3.weights
  ```

- yolov3-tiny

  ```
  python convert_weights_pb.py
  --class_names coco.names
  --data_format NHWC
  --weights_file yolov3-tiny.weights
  --tiny
  ```

### /Deep_sort

- [https://github.com/nwojke/deep_sort](https://github.com/nwojke/deep_sort)

### SORT

- [https://github.com/abewley/sort](https://github.com/abewley/sort)

### opencv.py

- YOLOv3 + NCS2 + opencv

### vino.py

- YOLOv3 + NCS2 + openvino

### sort_vino.py

- YOLOv3 + NCS2 + SORT

*추가적으로 사람만 판별하는 추론기를 만들어야 하고 그 후에 사람을 카운팅*
*현재 계속 진행중이다..*

---

# 참조
- [https://github.com/guillelopez/python-traffic-counter-with-yolo-and-sort](https://github.com/guillelopez/python-traffic-counter-with-yolo-and-sort)
- [https://github.com/jjeamin/OpenVINO-YoloV3](https://github.com/jjeamin/OpenVINO-YoloV3)
- [https://github.com/abewley/sort](https://github.com/abewley/sort)
- [https://github.com/nwojke/deep_sort](https://github.com/nwojke/deep_sort)
