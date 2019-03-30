---
layout: post
title:  "YOLO openvino NCS2"
summary: "NCS2에서 YOLO실행 using Raspberry"
date:   2019-03-30 13:00 -0400
categories: yolo,pi
---

## 구성
- Raspberry PI 3(라즈비안)
- WebCam
- NCS2

## Dependency
- python 3
- opencv 4.0.0
- openvino

## YOLOv3 모델 다운로드 받기
- [YOLOv3 weight](https://pjreddie.com/media/files/yolov3.weights)

- [YOLOv3-tiny weight](https://pjreddie.com/media/files/yolov3-tiny.weights)

- [coco.names](https://raw.githubusercontent.com/pjreddie/darknet/master/data/coco.names)

## pb 변환기 실행

1. [깃허브](https://github.com/mystic123/tensorflow-yolo-v3) 다운로드
```
git clone https://github.com/mystic123/tensorflow-yolo-v3.git
```


2. checkout
```
cd tensorflow-yolo-v3
git checkout ed60b90
```

3. 변환 실행

- yolov3
```
python3 convert_weights_pb.py --class_names coco.names --data_format NHWC --weights_file yolov3.weights
```

- yolov3-tiny
```
python3 convert_weights_pb.py --class_names coco.names --data_format NHWC --weights_file yolov3-tiny.weights --tiny
```

## 변환하고 summary 확인



![summary](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/intel/yolo_summary.PNG)



## IR 변환기 실행

```
python mo_tf.py --input_model yolov3.pb --input_shape [1,416,416,3] --tensorflow_use_custom_operations_config extensions/front/tf/yolo_v3.json
```

- input shape는 summary에서 봤듯이 batch size가 -1로 되어있었기 때문에 잘 조정하시면 됩니다.
- bin,xml 이 잘 만들어졌으면 라즈베리파이로 가져가서 실행을 시켜봅시다.

---
## YOLOv1,v2 모델 다운로드 받기

- [YOLOv1](https://pjreddie.com/darknet/yolov1/)
- [YOLOv2](https://pjreddie.com/darknet/yolov2/)

- [[config file](https://github.com/pjreddie/darknet)] 여기서 cfg폴더를 가져오면 됩니다.


## pb 변환기 실행
1. [깃허브](https://github.com/thtrieu/darkflow#dependencies) 다운로드
```
git clone https://github.com/thtrieu/darkflow.git
```

2. darkflow 설치

- [READ](https://github.com/thtrieu/darkflow/blob/master/README.md#getting-started)

- darkflow를 시작하려면 모듈을 설치해야한다.
```
cd darkflow
python setup.py build_ext --inplace
pip install .
```

3. 변환 실행
```
python ./flow --model <path_to_model>/<model_name>.cfg --load <path_to_model>/<model_name>.weights --savepb
```

## IR 변환기 실행
```
python3 ./mo_tf.py
--input_model <path_to_model>/<model_name>.pb       \
--batch 1                                       \
--tensorflow_use_custom_operations_config <OPENVINO_INSTALL_DIR>/deployment_tools/model_optimizer/extensions/front/tf/yolo_v1_v2.json
```



# 참조
- [https://github.com/PINTO0309/OpenVINO-YoloV3](https://github.com/PINTO0309/OpenVINO-YoloV3)
- [https://richardstechnotes.com/2018/12/01/running-yolov3-with-openvino-on-cpu-and-not-ncs-2/](https://richardstechnotes.com/2018/12/01/running-yolov3-with-openvino-on-cpu-and-not-ncs-2/)
- [https://software.intel.com/en-us/articles/OpenVINO-IE-Samples#object-detection-SSD-showcase](https://software.intel.com/en-us/articles/OpenVINO-IE-Samples#object-detection-SSD-showcase)
- [https://software.intel.com/en-us/articles/OpenVINO-Using-TensorFlow#converting-a-darknet-yolo-model](https://software.intel.com/en-us/articles/OpenVINO-Using-TensorFlow#converting-a-darknet-yolo-model)
- [http://cocodataset.org/#overview](http://cocodataset.org/#overview)
