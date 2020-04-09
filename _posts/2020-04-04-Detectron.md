---
layout: post
title:  "Detectron"
summary: "Pytorch Detectron 사용하기"
date:   2020-04-04 13:00 -0400
categories: paper
use_math: true
---

*오랜만에 포스트를 작성한다. 핑계를 말하자면.. 요즘 논문 작성 때문에 논문 읽기와 아이디어 생산에 집중하였다.. 결과는 좋지 않아 답답한 마음에 오랜만에 포스팅을 한다. 후에 논문을 쓰면서 했던 내 생각들을 딱 한편의 글로 작성할 예정이다. 다른 이들에게 도움이 되길..*

# Detectron
페이스북에서 개발한 `object detection`, `segmentation` 프레임워크다.

Pytorch로 구현된 Detectron 오픈소스가 2가지가 있다.

- [Detectron](https://github.com/roytseng-tw/Detectron.pytorch)
- [Detectron 2](https://github.com/facebookresearch/detectron2)

이 포스트에서는 구버전은 사용하지 않고 최신버전인 Detectron2를 사용한다. 지속적으로 업데이트 되고 공식 문서가 잘되어있다.

Detectron2 하기 위해서는

- python >= 3.6
- pytorch >= 1.3
- opencv, optional
- pycocotools

```
pip install cython; pip install -U 'git+https://github.com/cocodataset/cocoapi.git#subdirectory=PythonAPI'
```

가 필요하다.

## 설치

공식 설치하는데 문제가 많아 한줄씩 해결한 방법이다.

- PyYAML 설치

*ERROR: Cannot uninstall 'PyYAML'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.*

```
pip install conan --ignore-installed PyYAML  
```


- fvcore 설치

*ERROR: Could not find a version that satisfies the requirement fvcore (from detectron2==0.1.1) (from versions: none)
ERROR: No matching distribution found for fvcore (from detectron2==0.1.1)*

```
pip install -U 'git+https://github.com/facebookresearch/fvcore'
```

- detectron 설치

```
git clone https://github.com/facebookresearch/detectron2.git
cd detectron2 && python -m pip install -e .
```

만약 재빌드 할경우

```
rm -rf build/ **/*.so
```

## Demo

간단한 예제를 실행시켜보자

[model zoo](https://github.com/facebookresearch/detectron2/blob/master/MODEL_ZOO.md)에서 모델을 하나 다운로드 받아보자.

나는 object detection을 위해 다운로드 받았기 때문에 **R101-FPN** 을 다운로드 할 것이다. (ResNet101-FPN)

- 성능파일

```
wget https://dl.fbaipublicfiles.com/detectron2/COCO-Detection/faster_rcnn_R_101_FPN_3x/137851257/metrics.json
```

- 모델파일

```
wget https://dl.fbaipublicfiles.com/detectron2/COCO-Detection/faster_rcnn_R_101_FPN_3x/137851257/model_final_f6e8b1.pkl
```

모델 폴더를 따로 만들어두어 저장하면 편할것이다. 난 따로 모델 폴더를 하나 만들었다.

- 테스트할 이미지 파일

```
wget http://farm4.staticflickr.com/3775/9332963028_598bcb7aac_z.jpg
```



![input](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/detectron/input.jpg)



동작을 확인하기 위해서 train/test 상관없이 coco dataset에서 이미지를 가져온다.

- 데모실행

```
cd demo
```

```
python demo.py --config-file ../configs/COCO-Detection/faster_rcnn_R_101_FPN_3x.yaml --input ./1.jpg --opts MODEL.WEIGHTS [INPUT MODEL PATH]
```

- 추가

```
ROI CUDA 에러가 발생.. (한줄한줄이 에러..)
```

문제 해결 방법(1시간 정도 소비..)

환경 변수가 잘 맞지 않은 문제다.

먼저 `CUDA version = 10.1`로 진행해야하지만 컴퓨터에 여러 버전의 CUDA가 설치되어있기에 환경 변수가 잘못 들어 갔었다..

```
export CUDA_HOME=/usr/local/cuda-10.1
rm -rf build/
python setup.py build install
```

기본에 충실하자.


## 결과



![output](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/detectron/output.png)



잘나올것이다 ㅎㅎ


## 가벼운 API 사용법

- [Document](https://detectron2.readthedocs.io/)를 참고하면 도움이 될것 같다.

기존에 사용한 모델들을 이용해서 API형식으로 사용해보자

```python
from detectron2.engine import DefaultPredictor
from detectron2.config import get_cfg
from detectron2.utils.visualizer import Visualizer
from detectron2.data import MetadataCatalog
import cv2

# load image
img = cv2.imread('./detectron2/1.jpg')

# set config
cfg = get_cfg()
cfg.merge_from_file("./detectron2/configs/COCO-Detection/faster_rcnn_R_101_FPN_3x.yaml")
cfg.MODEL.ROI_HEADS.SCORE_THRESH_TEST = 0.5
cfg.MODEL.WEIGHTS = "./detectron2/model_final_f6e8b1.pkl"

# predict
predictor = DefaultPredictor(cfg)
outputs = predictor(img)

# visualization
v = Visualizer(img[:,:,::-1], MetadataCatalog.get(cfg.DATASETS.TRAIN[0]), scale=1.2)
v = v.draw_instance_predictions(outputs["instances"].to("cpu"))

cv2.imwrite('output.jpg',v.get_image()[:,:,::-1])
```

위에 Demo와 같게 출력된다.

## 참조
- [https://detectron2.readthedocs.io/](https://detectron2.readthedocs.io/)
- [https://gilberttanner.com/blog/detectron-2-object-detection-with-pytorch](https://gilberttanner.com/blog/detectron-2-object-detection-with-pytorch)
- [https://github.com/facebookresearch/detectron2](https://github.com/facebookresearch/detectron2)
