---
layout: post
title:  "Reinforcement[4]"
summary: "모두의 딥러닝 Reinforcement"
date:   2019-03-01 15:00 -0400
categories: Reinforcement
---

## Dependency
- python
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

## Deterministic vs Stochastic
- Deterministic : 행동이 일정함
- **Stochastic** : 행동이 일정하지 않다.

```python
env = gym.make('FrozenLake-v0')
```

기존 코드에 추가 시킨다면 99퍼센트는 실패한다.

**Solution**
- 여러명의 이야기를 듣는다.
- 이야기를 조금만 듣는다.

```python
a = 0.1
Q(s,a) <= (1-a)Q(s,a) + a[r + maxQ(s',a')]
```

## source
```python
import gym
import numpy as np
import matplotlib.pyplot as plt
#from gym.envs.registration import register

env = gym.make('FrozenLake-v0')

# Initialize table with all zeros
Q = np.zeros([env.observation_space.n, env.action_space.n])
# Set learning parameters
dis = .99
learning_rate = 0.85
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
        Q[state, action] = (1-learning_rate)*Q[state, action] \
        + learning_rate*(reward + dis * np.max(Q[new_state, :]))

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
