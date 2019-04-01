---
layout: post
title:  "Reinforcement[3]"
summary: "모두의 딥러닝 Reinforcement"
date:   2019-02-25 22:00 -0400
categories: Reinforcement
---

## requirement
- python 3
- tensorflow
- OpenAI Gym

## Q-Learning(Table)
- 행동 선택 후 실행
- 보상받기
- Q 업데이트

```
Q(s,a) <= r + maxQ(s',a')
```

- 상태 업데이트

```
s => s'
```

## Exploit vs Exploration
- action을 조정해준다.
- Exploit : 현재값을 이용
- Exploration : 새로운값을 이용

**E-greedy**
- 확률적으로 랜덤하게 가도록 한다.

**decaying E-greedy**
- 확률적으로 랜덤하게 가도록 하지만 점점 랜덤 확률을 줄여나간다.

**add random noise**
- 랜덤한 노이즈를 추가한다.
- **랜덤한 방향**
- 반복될수록 랜덤 노이즈는 작아짐(영향x)

## discount future reward
- 보상 값이 같을때 어디로 갈지 애매하다.
- 미래 보상을 discount시킨다.
- **최적의 길** 로 간다.
- update 조정

---

바뀐부분
- action(random noise)

```python
action = np.argmax(Q[state, :] + np.random.randn(1,env.action_space.n) / (i+1))
```

- update(discount)

```python
Q[state, action] = reward + dis * np.max(Q[new_state, :])
```

## source

```python
import gym
import numpy as np
import matplotlib.pyplot as plt
from gym.envs.registration import register

env = gym.make('FrozenLake-v3')

# Initialize table with all zeros
Q = np.zeros([env.observation_space.n, env.action_space.n])
# Set learning parameters
dis = .99
num_episodes = 2000

# create lists to contain total rewards and steps per episode
rList = []
for i in range(num_episodes):
    # Reset environment and get first new observation
    state = env.reset()
    rAll = 0
    done = False

    # The Q-Table learning algorithm
    while not done:
        # choose an action by greedily (with noise) picking from Q table
        action = np.argmax(Q[state, :] + np.random.randn(1,env.action_space.n) / (i+1))

        # Get new state and reward from environment
        new_state, reward, done, _ = env.step(action)

        # Update Q-Table with new knowledge using learning rate
        Q[state, action] = reward + dis * np.max(Q[new_state, :])

        rAll += reward
        state = new_state
    rList.append(rAll)

print("Success rate: " + str(sum(rList) / num_episodes))
print("Final Q-Table Values")
print("LEFT DOWN RIGHT UP")
print(Q)

plt.bar(range(len(rList)), rList, color="red")
plt.show()
```

- [추가] e-greedy

```python
e = 1. / ((i//100)+1)

while not done:
  if np.random.rand(1) < e:
    action = env.action_space.sample()
  else:
    action = np.argmax(Q[state,:])
```
