---
title:  "Tensorflow 1.13.1 에서 JAVA, C#에 포팅할 모델을 만드는 방법"
summary: "Tensorflow 1.13.1 에서 JAVA, C#에 포팅할 모델을 만드는 방법"
date:   2021-07-01 09:10 -0400
categories: news
---

> 보통 제가 읽으려고 글을 쓰는데 혹시 부족한게 있다면 댓글이나 메일을 부탁드립니다. 개선하겠습니다.

오늘은 개인적인 일로 Tensorflow를 JAVA, C#에 포팅하기위해 모델을 저장하는 방법을 정리하려 합니다.

사실 사소한건데 맨날 안적어놓으면 또 찾느라 시간 낭비가 되네요 ㅠㅠ

TensorFlow 2.0 version 부터는 설명이 잘 나와 있어서 쉽게 쉽게 할 수 있지만 TensorFlow 1.13.1 version을 예전에 사용하고 지금도 2.0으로 다시 짜기 어려워서 가끔 쓰게 됩니다.

---

사실 출력이 여러개인 부분을 다루는게 제일 문제도 많기 때문에 출력을 여러개로 하는 모델을 사용합니다.

## Model

각 출력이 10개의 클래스를 가지며 출력이 5개인 모델 입니다.

```python
def get_model(input_shape, num_len=5, num_classes=10):
    input_tensor = Input(input_shape)
    x = input_tensor

    for i, n_cnn in enumerate([1, 2, 1, 2, 1]):
        with tf.name_scope('layer%d' % (i+1)) as scope:
            for j in range(n_cnn):
                x = Conv2D(32*2**min(i, 3),
                           kernel_size=3,
                           padding='same',
                           kernel_initializer='he_uniform',
                           name='cnn_%d_%d' % (i+1, j+1))(x)
                x = BatchNormalization(name='batch_norm_%d_%d' % (i+1, j+1))(x)
                x = Activation('relu',
                               name='activate_%d_%d' % (i+1, j+1))(x)
            x = MaxPooling2D(2,
                             name='maxpool_%d_%d' % (i+1, j+1))(x)

    x = Flatten()(x)
    output = []

    for i in range(num_len):
        output.append(Dense(num_classes,
                            activation='softmax',
                            name='dense_%d' % i)(x))

    model = Model(inputs=input_tensor, outputs=output)

    return model
```

학습이나 추론이 중점이 아니고 포팅할때 사용할 모델을 만드는데 초점이 있는 글이기에 학습과 추론은 생략합니다.

## JAVA

자바에서는 모델 폴더를 넣어주어야합니다..

```
model/
  variables/
    variables.data-00000-of-00001
    variables.index
  saved_model.pb
```

이건 ~

```python
import os
import numpy as np
import tensorflow as tf
import keras.backend as K
import matplotlib.pyplot as plt
import models

input_shape = (W, H, C)

load_path = "./model.h5"

# model
model = models.get_model(input_shape,
                         num_len=10,
                         num_classes=5)

model.load_weights(load_path)

signature = tf.saved_model.signature_def_utils.predict_signature_def(
        inputs={'image': model.input}, outputs={'0': model.output[0],
                                                '1': model.output[1],
                                                '2': model.output[2],
                                                '3': model.output[3],
                                                '4': model.output[4]})

builder = tf.saved_model.builder.SavedModelBuilder(os.path.join('./freeze_models', load_path))
legacy_init_op = tf.group(tf.tables_initializer(), name='legacy_init_op')

builder.add_meta_graph_and_variables(
        sess=K.get_session(),
        tags=[tf.saved_model.tag_constants.SERVING],
        main_op=tf.local_variables_initializer(),
        legacy_init_op=legacy_init_op,
        signature_def_map={
            tf.saved_model.signature_constants.DEFAULT_SERVING_SIGNATURE_DEF_KEY:
                signature
        })

builder.save()
```

`freeze_models` 폴더 안에 모델 이름으로 생성 된 폴더를 JAVA에서 실행시킬때 넣어주면 됩니다.

`outputs` 부분은 모델에 맞게 바꾸어주셔야 합니다.

---

## TensorFlowShape

- [https://github.com/migueldeicaza/TensorFlowSharp/](https://github.com/migueldeicaza/TensorFlowSharp/)

- `.pb` 모델을 넣어주어야합니다.

```python
from keras.optimizers import *
import tensorflow as tf
import keras.backend as K
import models

K.set_learning_phase(0)


def freeze_session(session, keep_var_names=None, output_names=None, clear_devices=True):
    graph = session.graph
    with graph.as_default():
        freeze_var_names = list(set(v.op.name for v in tf.global_variables()).difference(keep_var_names or []))
        output_names = output_names or []
        output_names += [v.op.name for v in tf.global_variables()]
        input_graph_def = graph.as_graph_def()
        if clear_devices:
            for node in input_graph_def.node:
                node.device = ""
        frozen_graph = tf.graph_util.convert_variables_to_constants(
            session, input_graph_def, output_names, freeze_var_names)
        return frozen_graph


def freeze(m, save_name):
    print([out.op.name for out in m.outputs])
    frozen_graph = freeze_session(K.get_session(),
                                  output_names=[out.op.name for out in m.outputs])
    tf.io.write_graph(graph_or_graph_def=frozen_graph,
                  logdir="./frozen_models",
                  name="frozen_graph.pb",
                  as_text=False)

    print("[INFO] Model h5 -> pb")

if __name__ == "__main__":
    # config
    config = tf.ConfigProto()
    config.gpu_options.allow_growth = True
    sess = tf.Session(config=config)
    sess.run(tf.global_variables_initializer())
    K.set_session(sess)

    input_shape = (W, H, C)
    tf.keras.backend.set_learning_phase(0)
    model = models.get_model(input_shape,
                             num_len=10,
                             num_classes=5)

    model.load_weights("./model.h5")
    freeze(model, './frozen_graph.pb')
```

`frozen_models` 폴더 안에 생성 된 `frozon_graph.pb` 파일을 C#에서 실행시킬때 넣어주면 됩니다.
