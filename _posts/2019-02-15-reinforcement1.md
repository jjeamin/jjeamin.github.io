---
layout: post
title:  "Reinforcement[1]"
summary: "모두의 딥러닝 Reinforcement"
date:   2019-02-15 15:00 -0400
categories: Reinforcement
---

# Reinforcement Learning
보상을 주는 학습법

- 로보틱스
  + 관절의 움직임
- 비즈니스
  + 재고 파악
- 금융
  + 주식
- 미디어
  + 컨텐츠
  + 광고



![example](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/reinforcement/example.JPG)



---

## Dependency
- python
- tensorflow
- OpenAI Gym

---

## OpenAI Gym
환경을 만들어주는 프레임 워크
- input : action
- output : state,reward
- game

---

## source

```python
import gym
from gym.envs.registration import register
import readchar # pip install readchar

# This is the original version, but not work on windows.
# ........

LEFT = 0
DOWN = 1
RIGHT = 2
UP = 3

arrow_keys = {
    '\x1b[A' : UP,
    '\x1b[B' : DOWN,
    '\x1b[C' : RIGHT,
    '\x1b[D' : LEFT
}

register(
    id='FrozenLake-v3',
    entry_point='gym.envs.toy_text:FrozenLakeEnv',
    kwargs={'map_name' : '4x4', 'is_slippery': False}
)

env = gym.make('FrozenLake-v3')
env.render()

while True:
    key = readchar.readkey()
    if key not in arrow_keys.keys():
        print("Game aborted!")
        break

    action = arrow_keys[key]
    state, reward, done, info = env.step(action)
    env.render()
    print("State: ", state, "Action: ", action, "Reward: ", reward, "Info: ", info)

    if done:
        print("Finished with reward", reward)
        break
```
