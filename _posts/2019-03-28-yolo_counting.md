---
layout: post
title:  "YOLO People counting"
summary: "라즈베리파이에서 NCS2를 이용해 yolo people counting"
date:   2019-03-28 13:00 -0400
categories: yolo
---

# YOLO People Counting

GitHub : [https://github.com/jjeamin/People_counting_yolo](https://github.com/jjeamin/People_counting_yolo)

---

# 구성
- raspberry pi 3 (rasbian)
- webcam
- NCS2

# requirement
- openvino toolkit
- opencvs
- sklearn
- filterpy
- imutils
- sort

# 필독!!
- [라즈베리파이에서 openvino 사용하기](https://jjeamin.github.io/pi/2019/03/08/NCS2/)
- [IR](https://jjeamin.github.io/pi/2019/03/08/NCS2_IR/)
- [IR - YOLO](https://jjeamin.github.io/openvino/2019/03/30/yolo-openvino/)

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

# 사용법

1. 위에 xml_path와 bin_path를 xml,bin 파일 경로로 바꾸어준다.
2. capture function에서 아래 코드 주석처리
```
# cv2.imwrite('test2.jpg',frame)
```
3. 스케쥴러를 이용해서 웹캠으로 10초에 한번씩 사람의 수를 counting한다.

```
python3 main.py
```

**logger**
1. 인터넷이 연결되어있는지 확인 -> 서버로 전송가능
2. 카메라가 연결되어있는지 확인

---

# Citation

**YOLO**

    @article{redmon2016yolo9000,
      title={YOLO9000: Better, Faster, Stronger},
      author={Redmon, Joseph and Farhadi, Ali},
      journal={arXiv preprint arXiv:1612.08242},
      year={2016}
    }

**SORT**

    @inproceedings{Bewley2016_sort,
      author={Bewley, Alex and Ge, Zongyuan and Ott, Lionel and Ramos, Fabio and Upcroft, Ben},
      booktitle={2016 IEEE International Conference on Image Processing (ICIP)},
      title={Simple online and realtime tracking},
      year={2016},
      pages={3464-3468},
      keywords={Benchmark testing;Complexity theory;Detectors;Kalman filters;Target tracking;Visualization;Computer Vision;Data Association;Detection;Multiple Object Tracking},
      doi={10.1109/ICIP.2016.7533003}
    }


# Reference
- [https://github.com/guillelopez/python-traffic-counter-with-yolo-and-sort](https://github.com/guillelopez/python-traffic-counter-with-yolo-and-sort)
- [https://github.com/jjeamin/OpenVINO-YoloV3](https://github.com/jjeamin/OpenVINO-YoloV3)
- [https://github.com/abewley/sort](https://github.com/abewley/sort)
