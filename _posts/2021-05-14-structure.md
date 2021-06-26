---
title:  "딥러닝 프로젝트 구조잡기"
summary: "어떻게 딥러닝 프로젝트 구조를 깔끔하게 잡을까.."
date:   2021-05-14 09:10 -0400
categories: pytorch
---

머신러닝 실험, 개발 등 다양한 작업을 하는데 딥러닝 프로젝트의 구조의 baseline의 필요성이 느껴졌다. (맨날 새로 프로젝트를 열면 구성이 일관되게 하기 위함)

크게 `src`, `data`, `docs`, `configs` 를 작성하는게 중요하지 않을까 생각한다.

다양한 구조를 잡는 방법이 있지만 아래 구조를 따라가도록 하며 계속 수정을 해야겠다.

Github : [Here](https://github.com/jjeamin/Deep_Learning_Project_Structure)

---

## /configs

- model configs
    + Choose the format
    + `.json`
    + `.ini`
    + `.yaml`
    + `.py`

다양한 설정파일이 존재하지만 `.json`, `.ini`, `.yaml`, `.py` 포멧을 많이 사용한다.

TODO
- 예제 파일 만들기
- 각 format에 대한 설명

---

## /data

- your datasets
    + train
        - class 1
        - class 2
        - etc.
    + valid
        - class 1
        - class 2
        - etc.
    + test
        - class 1
        - class 2
        - etc.

데이터셋을 구성하다보면 데이터 구조가 어지럽고 복잡한게 많다. 최대한 위에 구조를 따르면 좋을 것 같다.

TODO
- 데이터의 종류마다 예제 구조 만들기

---

## /docs

- project documents
    + install
    + how to run?
    + api structure

Documents는 실제로 사용해본적이 거의 없지만 큰 프로젝트들은 대략적으로 위에 형식을 가진다.

## /images

- save sample image
    + dataloader output image (+ transforms)
    + sample image for test
    + result

실제 개발 및 실험을 하다보면 sample을 확인해야하는 경우가 있다.

- 실제 개발된 제품에서 사용될 샘플
- 데이터 로더가 잘동작하는가?
- 모델의 출력이 정상적인가?

## /checkpoints

- your trained model

- 훈련된 모델은 각자의 이름 규칙을 가지고 저장하자.

## /notebooks

- data processing notebook
- tutorial notebook

## /scripts

- train scripts
- test scripts
- run scripts

## /test

For unittest

- dataset
- dataloader
- model
- train
- valid
- test

## /src

- `/data`
    + dataloader
    + data utils

- `/model`
    + model architecture
    + model utils

- `/visualization`
    + visualization tools

- `loss.py` and `/loss`
    + define custom loss

- `optim.py` and `/optim`
    + define custom optimizer

- `scheduler.py` and `/scheduler`
    + define custom scheduler

- `transforms.py` and `/transforms`
    + define custom data augmentation

## setup.py

```
import setuptools
# python setup.py develop
setuptools.setup(
    name            = "[NAME]", # Replace with your own username
    version         = "0.0.1",
    author          = "Jaemin Jeong",
    author_email    = "woalsdl600@gmail.com",
    description     = "[description]",
    url             = "[Github repository url]",
    project_urls    = {"Bug Tracker": "[Github repository url]/issues",
    },
    classifiers     = [
        "Programming Language :: Python :: 3",
        "License :: OSI Approved :: MIT License",
        "Operating System :: OS Independent",
    ],
    python_requires = ">=3.6",
)
```
