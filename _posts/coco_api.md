---
layout: post
title:  "COCO API 사용하기"
summary: "python에서 COCO API를 사용해보자"
date:   2019-05-16 12:00 -0400
categories: COCO
---
# API
- 응용 프로그램 프로그래밍 인터페이스

# API 다운로드 받기

- 리눅스

```
git clone https://github.com/cocodataset/cocoapi.git
cd PythonAPI
make
```

make에서 c오류가 날 경우

```
pip install cython
```

- window

visual studio 2015를 다운로드 받아야합니다.

```
git clone https://github.com/cocodataset/cocoapi.git
cd PythonAPI
```

그리고 window의 오류가 발생하기 때문에 setup.py를 수정해야한다.

```
extra_compile_args=['-Wno-cpp', '-Wno-unused-function', '-std=c99']

에서

extra_compile_args={'gcc': ['/Qstd=c99']
```

수정이 완료가 되었다면 make를 진행하면 된다.

```
python setup.py build_ext install
python setup.py build_ext --inplace
```


# images와 Annotations 다운로드 받기

- [HERE](http://cocodataset.org/#download)

엄청 오래걸린다.. 저같은 경우는 다운로드가 계속 잘 안되서 리눅스에서 `google cloud service`를 사용해 다운로드 했습니다. 속도가 빠르다는 장점이 있어요

```
$ sudo apt install curl
$ curl https://sdk.cloud.google.com | bash
$ source ~/.bashrc

gsutil -m rsync gs://images.cocodataset.org/train2017 train2017
gsutil -m rsync gs://images.cocodataset.org/test2017 test2017
gsutil -m rsync gs://images.cocodataset.org/val2017 val2017
gsutil -m rsync gs://images.cocodataset.org/annotations annotations : error!!
wget -c http://images.cocodataset.org/annotations/annotations_trainval2017.zip
```


# 참조
- [https://github.com/cocodataset/cocoapi.git](https://github.com/cocodataset/cocoapi.git)
- [http://cocodataset.org/#download](http://cocodataset.org/#download)
