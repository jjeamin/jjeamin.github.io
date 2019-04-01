---
layout: post
title:  "Reinforcement[2]"
summary: "모두의 딥러닝 Reinforcement"
date:   2019-02-25 20:00 -0400
categories: Reinforcement
---

## requirement
- python 3
- tensorflow
- OpenAI Gym

## Q-Learning(Table)

![q](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/reinforcement/q.JPG)

최대로 보상 받을수 있는 방향을 알려줘~

### 원리

```
MAX Q = maxQ(state,action)

R = r1 + r2 + r3 + r4 + ''' + rn
R(t) = r(t) + r(t+1) + ''' + rn
R(t) = r(t) + R(t+1)
Q(s,a) <= r + maxQ(s',a')
```

state,action,reward ~ state,action,reward

### 알고리즘
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

---

## source

```python
import gym
import numpy as np
import matplotlib.pyplot as plt
from gym.envs.registration import register
import random as pr

def rargmax(vector):    # https://gist.github.com/stober/1943451
    """ Argmax that chooses randomly among eligible maximum idices. """
    m = np.amax(vector)
    indices = np.nonzero(vector == m)[0]
    return pr.choice(indices)

'''
register(
    id='FrozenLake-v1',
    entry_point='gym.envs.toy_text:FrozenLakeEnv',
    kwargs={'map_name' : '4x4', 'is_slippery': False}
)
'''

env = gym.make('FrozenLake-v3')

# Initialize table with all zeros
Q = np.zeros([env.observation_space.n, env.action_space.n])
# Set learning parameters
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
        action = rargmax(Q[state, :])

        # Get new state and reward from environment
        new_state, reward, done, _ = env.step(action)

        # Update Q-Table with new knowledge using learning rate
        Q[state, action] = reward + np.max(Q[new_state, :])

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
