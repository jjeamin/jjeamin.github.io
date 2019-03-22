---
layout: post
title:  "Tensorflow 2.0"
summary: "텐서플로우 2.0 사용하기"
date:   2019-03-21 22:00 -0400
categories: tensorflow
---

# TensorFlow 2.0 alpha
이번에 tensorflow에서 2.0 alpha 버전이 새로 릴리즈 되었습니다. 케라스과 연동을 더욱 더 강화했다고 하는데 설치하면서 알아보겠습니다.


# 설치
```
pip install tensorflow==2.0.0-alpha0
```

# 바뀐거 실감하기

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
