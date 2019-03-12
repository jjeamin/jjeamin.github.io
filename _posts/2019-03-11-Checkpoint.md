---
layout: post
title:  "tensorflow 모델 저장하기"
summary: "tensorflow checkpoint"
date:   2019-03-11 22:00 -0400
categories: tensorflow
---

# 저장할것
- checkpoint
- graph
- tensorboard

# Checkpoint

```python
saver = tf.train.Saver()
```

## save

```python
checkpoint_dir = "HERE/DIR/NAME"

if not os.path.exists(checkpoint_dir)
  os.makedir(checkpoint_dir)

saver.save(sess,os.path.join(checkpoint_dir, "NAME.ckpt"),global_step=0)
```

save 함수

- 첫번째 인자 : sess
- 두번째 인자 : ckpt 파일 경로
- 세번째 인자 : 몇회마다 저장할지에 대한 step

## load
```python
import re

ckpt = tf.train.get_checkpoint_state(checkpoint_dir)

if ckpt and ckpt.model_checkpoint_path:
    ckpt_name = os.path.basename(ckpt.model_checkpoint_path)

    saver.restore(sess, os.path.join(checkpoint_dir, ckpt_name))
    # 파일 이름에서 몇회부터 시작할지 추출
    counter = int(next(re.finditer("(\d+)(?!.*\d)", ckpt_name)).group(0))
    print("Success")
else:
    print("Failed")
```

restore 함수

- 첫번째 인자 : sess
- 두번째 인자 : ckpt 파일 경로

# graph

```python
tf.train.write_graph(sess.graph_def, '.', 'graph.pbtxt')
```

write_graph 함수

- 첫번째 인자 : sess.graph_def
- 두번째 인자 : 저장 경로
- 세번째 인자 : 저장 파일 이름
