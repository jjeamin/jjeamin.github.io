---
layout: post
title:  "YOLO openvino NCS2"
summary: "NCS2에서 YOLO실행 using Raspberry"
date:   2019-03-30 13:00 -0400
categories: openvino
---

## Dependency
- raspberry pi 3
- Neural Compute Stick 2
- WebCam

## requirement
- opencv 4.0.0
- [openvino](https://jjeamin.github.io/pi/2019/03/08/NCS2/)

---

# YOLOv3
yolov3를 사용할 경우에 보기

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

## 변환하고 summary 확인



![summary](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/intel/yolo_summary.PNG)



## IR 변환기 실행

```
python mo_tf.py
--input_model yolov3.pb
--input_shape  [1,416,416,3]
--data_type FP16
--tensorflow_use_custom_operations_config extensions/front/tf/yolo_v3.json
```

- input shape는 summary에서 봤듯이 batch size가 -1로 되어있었기 때문에 잘 조정하시면 됩니다.
- bin,xml 이 잘 만들어졌으면 라즈베리파이로 가져가서 실행을 시켜봅시다.


---


# YOLOv1,v2
yolov1,v2를 사용하고 싶을때 보기


## YOLOv1,v2 모델 다운로드 받기

- [YOLOv1](https://pjreddie.com/darknet/yolov1/)
- [YOLOv2](https://pjreddie.com/darknet/yolov2/)

- [Config File](https://github.com/pjreddie/darknet) : 여기서 cfg폴더를 가져오면 됩니다.


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
python ./flow
--model <path_to_model>/<model_name>.cfg
--load <path_to_model>/<model_name>.weights
--savepb
```

## IR 변환기 실행
```
python ./mo_tf.py
--input_model <path_to_model>/<model_name>.pb       \
--batch 1                                       \
--data_type FP16
--tensorflow_use_custom_operations_config <OPENVINO_INSTALL_DIR>/deployment_tools/model_optimizer/extensions/front/tf/yolo_v1_v2.json
```

---

이제 라즈베리파이에 올려서 실행을 시켜보도록 하자. 라즈베리파이에 올리기 위한 `.xml`,`.bin`을 라즈베리파이로 옮기고 그 후에 간단한 테스트를 진행해볼것이다.

## 단순 opencv를 이용한 테스트

```python
import cv2 as cv
import time

xml_path = '/home/pi/workspace/IR/tiny-yolov3.xml'
bin_path = '/home/pi/workspace/IR/tiny-yolov3.bin'

# Load the model
net = cv.dnn.readNet(xml_path, bin_path)
# Specify target device
net.setPreferableTarget(cv.dnn.DNN_TARGET_MYRIAD)


# Read an image
frame = cv.imread('test.jpeg')
frame = cv.resize(frame,(416,416))

# Prepare input blob and perform an inference
blob = cv.dnn.blobFromImage(frame, size=(416, 416), ddepth=cv.CV_8U)
net.setInput(blob)
start = time.time()
out = net.forward()
end = time.time()

print("inference time : ",(end - start))
```

---

## openvino IE엔진을 이용한 테스트

**MYRIAD.. NCS2는 FP32는 지원하지 않습니다..**
inference를 NCS2로 진행하기 위해서는 `data_type`이 FP16이어야 한다. FP32는 지원을 하지 않기 때문에 꼭 `.xml`,`.bin`으로 변환시킬때 `data_type`을 FP16으로 해야합니다. 이것때문에 귀찮은 일을 반복했네요..

```python
from openvino.inference_engine import IENetwork, IEPlugin
import numpy as np
import cv2 as cv
import time

xml_path = '/home/pi/workspace/IR/tiny-yolov3.xml'
bin_path = '/home/pi/workspace/IR/tiny-yolov3.bin'

# network 생성
net = IENetwork(model = xml_path,weights = bin_path)
'''
print("input : ",net.inputs)
print("input shape :",net.inputs['inputs'].shape)
print("output : ",net.outputs.keys())
print("output shape :",net.outputs['detector/yolo-v3-tiny/Conv_9/BiasAdd/YoloRegion'].shape)
print("output shape :",net.outputs['detector/yolo-v3-tiny/Conv_12/BiasAdd/YoloRegion'].shape)

print("net layer :",*list(net.layers.keys()),sep='\n')
'''
# device에
plugin = IEPlugin(device='MYRIAD')
exec_net = plugin.load(net)

start = time.time()

frame = cv.imread('test.jpeg')
resized_image = cv.resize(frame, (416, 416), interpolation = cv.INTER_CUBIC)
prepimg = resized_image[np.newaxis, :, :, :]     # Batch size axis add
# position trans
prepimg = prepimg.transpose((0, 3, 1, 2))  # NHWC to NCHW

end = time.time()

print('image process time : ',end - start)

start = time.time()

# inference
res = exec_net.infer({'inputs':prepimg})

end = time.time()

print('inference time : ',end-start)
```

## 결과



![summary](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/intel/inference.PNG)



**둘다 계속돌려보니까 알게된건데 처음 추론할 때 opencv가 느리게 보이지만 계속 추론시키면 시간 차이가 그렇게 크지 않습니다.**

---

# 참조

## YOLO 모델 생성
- [https://github.com/PINTO0309/OpenVINO-YoloV3](https://github.com/PINTO0309/OpenVINO-YoloV3)
- [https://richardstechnotes.com/2018/12/01/running-yolov3-with-openvino-on-cpu-and-not-ncs-2/](https://richardstechnotes.com/2018/12/01/running-yolov3-with-openvino-on-cpu-and-not-ncs-2/)
- [https://software.intel.com/en-us/articles/OpenVINO-IE-Samples#object-detection-SSD-showcase](https://software.intel.com/en-us/articles/OpenVINO-IE-Samples#object-detection-SSD-showcase)
- [https://software.intel.com/en-us/articles/OpenVINO-Using-TensorFlow#converting-a-darknet-yolo-model](https://software.intel.com/en-us/articles/OpenVINO-Using-TensorFlow#converting-a-darknet-yolo-model)
- [http://cocodataset.org/#overview](http://cocodataset.org/#overview)


## 추론
- [http://docs.openvinotoolkit.org/latest/_ie_bridges_python_docs_api_overview.html](http://docs.openvinotoolkit.org/latest/_ie_bridges_python_docs_api_overview.html)
