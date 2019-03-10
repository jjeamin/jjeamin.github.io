---
layout: post
title:  "Reinforcement[5]"
summary: "모두의 딥러닝 Reinforcement"
date:   2019-03-01 16:00 -0400
categories: Reinforcement
---

## Dependency
- python
- tensorflow
- OpenAI Gym

## Q-Network



![net](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/reinforcement/net1.JPG)



network를 이용하자

## algorithm



![algo](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/reinforcement/algo.JPG)



- e-greed 사용 : **select action**

```python
e = 1. / ((i//100)+1)

while not done:
  if np.random.rand(1) < e:
    action = env.action_space.sample()
  else:
    action = np.argmax(Q[state,:])
```

## one-hot : input

```python
np.identity(16)[s1:s2 + 1]
```

## source

```python
import gym
import matplotlib.pyplot as plt
import tensorflow as tf
import numpy as np

def one_hot(x):
    return np.identity(16)[x:x+1]

# make env
env = gym.make('FrozenLake-v0')

# parameter
# 16
input_size = env.observation_space.n
# 4
output_size = env.action_space.n
learning_rate = 0.1
dis = .99
num_episodes = 2000


# input,weight
X = tf.placeholder(shape=[1,input_size],dtype=tf.float32)
W = tf.Variable(tf.random_uniform([input_size,output_size],0,0.01))

# output,y label
Qpred = tf.matmul(X,W)
Y = tf.placeholder(shape=[1,output_size],dtype=tf.float32)

# loss
loss = tf.reduce_mean(tf.square(Y-Qpred))
# train
train = tf.train.GradientDescentOptimizer(learning_rate = learning_rate).minimize(loss)

rList = []

with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    for i in range(num_episodes):
        state = env.reset()
        e = 1./((i/50) + 10)
        rAll = 0
        done = False
        local_loss = []

        while not done:
            Qs = sess.run(Qpred,feed_dict={X: one_hot(state)})

            # e-greed select action
            if np.random.rand(1) < e:
                a = env.action_space.sample()
            else:
                a = np.argmax(Qs)

            # get state,reward
            state1, reward, done, _ = env.step(a)

            # Update
            if done:
                Qs[0,a] = reward
            else:
                Qs1 = sess.run(Qpred,feed_dict={X: one_hot(state1)})
                Qs[0,a] = reward + dis * np.max(Qs1)

            # train
            sess.run(train,feed_dict={X: one_hot(state), Y: Qs})

            rAll += reward
            state = state1

        rList.append(rAll)
        print(rAll)

        #print("step[" + str(num_episodes) + "/" + str(i) + "]")

print("percent of episodes: " + str(sum(rList) / num_episodes) + "%")
plt.bar(range(len(rList)),rList,color="red")
plt.show()
```
