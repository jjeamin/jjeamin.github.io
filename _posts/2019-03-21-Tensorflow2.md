---
layout: post
title:  "Tensorflow 2.0"
summary: "텐서플로우 2.0 사용하기"
date:   2019-03-21 22:00 -0400
categories: tensorflow
---

# TensorFlow 2.0 alpha
이번에 tensorflow에서 2.0 alpha 버전이 새로 릴리즈 되었습니다. 케라스과 연동을 더욱 더 강화했다고 하는데 설치하면서 알아보겠습니다.

---

# 설치
```
conda create -n alpha python=3.5

pip install -q tensorflow==2.0.0-alpha0
```

# 바뀐부분

- session을 경량화 했다.

```python
# TensorFlow 1.x
outputs = session.run(f(placeholder), feed_dict={placeholder: input})

# TensorFlow 2.0
outputs = f(input)
```

- model을 keras에 중점을 맞추었다.

# 바뀐거 맛만 보기

- TensorFlow 1.x [Before]

```python
in_a = tf.placeholder(dtype=tf.float32, shape=(2))
in_b = tf.placeholder(dtype=tf.float32, shape=(2))

def forward(x):
  with tf.variable_scope("matmul", reuse=tf.AUTO_REUSE):
    W = tf.get_variable("W", initializer=tf.ones(shape=(2,2)),
                        regularizer=tf.contrib.layers.l2_regularizer(0.04))
    b = tf.get_variable("b", initializer=tf.zeros(shape=(2)))
    return x * train_data + b

out_a = model(in_a)
out_b = model(in_b)

reg_loss = tf.losses.get_regularization_loss(scope="matmul")

with tf.Session() as sess:
  sess.run(tf.global_variables_initializer())
  outs = sess.run([out_a, out_b, reg_loss],
                feed_dict={in_a: [1, 0], in_b: [0, 1]})

```

- TensorFlow 2.x []

```python
W = tf.Variable(tf.ones(shape=(2,2)), name="W")
b = tf.Variable(tf.zeros(shape=(2)), name="b")

@tf.function
def forward(x):
  return W * x + b

out_a = forward([1,0])
print(out_a)

out_b = forward([0,1])

regularizer = tf.keras.regularizers.l2(0.02)
reg_loss = regularizer(W)
```

# 간단한 ML

- 이미지 픽셀 범위 확인

```
plt.figure()
plt.imshow(train_images[0])
plt.colorbar()
plt.grid(False)
plt.show()
```

- 이미지 전처리

```
train_images = train_images / 255.0

test_images = test_images / 255.0
```

# Classification(분류)
```python
from __future__ import absolute_import, division, print_function, unicode_literals

import numpy as np
import matplotlib.pyplot as plt
import tensorflow as tf
from tensorflow import keras

print(tf.__version__)

def plot_image(i, predictions_array, true_label, img):
    predictions_array, true_label, img = predictions_array[i], true_label[i], img[i]
    plt.grid(False)
    plt.xticks([])
    plt.yticks([])

    plt.imshow(img, cmap=plt.cm.binary)

    predicted_label = np.argmax(predictions_array)
    if predicted_label == true_label:
        color = 'blue'
    else:
        color = 'red'

    plt.xlabel("{} {:2.0f}% ({})".format(class_names[predicted_label],
                                         100 * np.max(predictions_array),
                                         class_names[true_label]),
               color=color)


def plot_value_array(i, predictions_array, true_label):
    predictions_array, true_label = predictions_array[i], true_label[i]
    plt.grid(False)
    plt.xticks([])
    plt.yticks([])
    thisplot = plt.bar(range(10), predictions_array, color="#777777")
    plt.ylim([0, 1])
    predicted_label = np.argmax(predictions_array)

    thisplot[predicted_label].set_color('red')
    thisplot[true_label].set_color('blue')

# Dataset
fashion_mnist = keras.datasets.fashion_mnist.load_data()

# fashion Dataset
(train_images, train_labels), (test_images,test_labels) = fashion_mnist

# Class
class_names = ['T-shirt/top', 'Trouser', 'Pullover', 'Dress', 'Coat',
               'Sandal', 'Shirt', 'Sneaker', 'Bag', 'Ankle boot']

# Shape, Label
print("Train Shape : " ,train_images.shape)
print("Train Label : " ,len(train_labels))

print("Test Shape : " ,test_images.shape)
print("Test Label : " ,len(test_labels))

# Preprossing
train_images = train_images / 255.0

test_images = test_images / 255.0

# model
model = keras.Sequential([
    keras.layers.Flatten(input_shape=(28, 28)),
    keras.layers.Dense(128, activation='relu'),
    keras.layers.Dense(10, activation='softmax')
])

# compile
model.compile(optimizer='adam',
              loss='sparse_categorical_crossentropy',
              metrics=['accuracy'])

# train
model.fit(train_images, train_labels, epochs=5)

# accuracy
test_loss, test_acc = model.evaluate(test_images, test_labels)

print('\n테스트 정확도:', test_acc)

# predictions
predictions = model.predict(test_images)

i = 11

print('prediction[Argmax] : ', np.argmax(predictions[i]))
print('ground-truth : ',test_labels[i])

# batch make
img = (np.expand_dims(test_images[i],0))

print(img.shape)

# batch prediction
predictions_single = model.predict(img)

plt.subplot(1,2,1)
plt.grid(False)
plt.imshow(img[0], cmap=plt.cm.binary)
plt.subplot(1,2,2)
plot_value_array(0, predictions_single, test_labels)
_ = plt.xticks(range(10), class_names, rotation=45)
plt.show()

print('prediction[Argmax] : ', np.argmax(predictions_single[0]))
print('ground-truth : ',test_labels[i])
```

# 결과




![result](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/tensorflow/class.PNG)
