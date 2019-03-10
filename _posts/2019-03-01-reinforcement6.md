---
layout: post
title:  "Reinforcement[6]"
summary: "모두의 딥러닝 Reinforcement"
date:   2019-03-01 20:00 -0400
categories: Reinforcement
---

## Dependency
- python
- tensorflow
- OpenAI Gym

## CartPole Game Start

```python
import gym
env = gym.make('CartPole-v0')
env.reset()

reward_sum = 0

for _ in range(10):
    env.render()
    action = env.action_space.sample()
    observation, reward, done, _ = env.step(action)
    print(observation,reward)
    reward_sum += reward
    if done:
        print("reward : ",reward_sum)
        reward_sum = 0
        env.reset()
```

## source
- 기존 FrozenLake는 input이 한개였고 CartPole은 input의 값이 **4개**  이기 때문에 변경
- output도 2개로 변경
- 행동이 안좋은 방향으로 갈때 보상 -100

```python
import gym
import tensorflow as tf
import numpy as np

# make env
env = gym.make('CartPole-v0')

# parameter

# 4개
input_size = env.observation_space.shape[0]
# 2개
output_size = env.action_space.n
learning_rate = 1e-1
dis = .9
num_episodes = 2000


# input,weight
X = tf.placeholder(shape=[None,input_size],dtype=tf.float32)
W1 = tf.get_variable("W1",[input_size,output_size],initializer=tf.contrib.layers.xavier_initializer())

# output,y label
Qpred = tf.matmul(X,W1)
Y = tf.placeholder(shape=[1,output_size],dtype=tf.float32)

# loss
loss = tf.reduce_mean(tf.square(Y-Qpred))
# train
train = tf.train.AdamOptimizer(learning_rate = learning_rate).minimize(loss)

rList = []

with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    for i in range(num_episodes):
        state = env.reset()
        e = 1./((i/10) + 1)
        rAll = 0
        step_count = 0
        done = False
        local_loss = []

        while not done:
            step_count += 1
            x = np.reshape(state,[1,input_size])
            Qs = sess.run(Qpred,feed_dict={X: x})

            # e-greed select action
            if np.random.rand(1) < e:
                a = env.action_space.sample()
            else:
                a = np.argmax(Qs)

            # get state,reward
            state1, reward, done, _ = env.step(a)

            # Update
            if done:
                Qs[0,a] = -100
            else:
                x1 = np.reshape(state1,[1,input_size])
                Qs1 = sess.run(Qpred,feed_dict={X: x1})
                Qs[0,a] = reward + dis * np.max(Qs1)

            # train
            sess.run(train,feed_dict={X: x, Y: Qs})
            state = state1

        rList.append(step_count)
        print("step : " + str(step_count))
        if len(rList) > 10 and np.mean(rList[-10:]) > 500:
            break


    observation = env.reset()
    reward_sum = 0

    while True:
        env.render()

        x = np.reshape(observation, [1, input_size])
        Qs = sess.run(Qpred, feed_dict={X: x})
        action = np.argmax(Qs)

        observation, reward, done, _ = env.step(action)
        reward_sum += reward
        if done:
            print("reward : ",reward_sum)
            break
```
