---
layout: post
title:  "Linear Algebra"
summary: "그냥 훑어보기 좋은 선형 대수학 + PCA, LDA"
date:   2020-01-11 16:00 -0400
categories: math
---

## 벡터

- vector = 크기 + 방향
- **순서가 정해져 있다.**
- one-dimension
- lowercase
- 표현 : $$x = [1,2,3] \in \mathbb{R}^3$$

## 행렬
- row vector, column vertor
- 행렬곱 : $$AB \neq BA$$
- uppercase

## linear equation

$$a_1 x_1 + a_2 x_2 + \cdots + a_n x_n = b$$

$$a^T x = b$$ 위에 식과 같은식인데 transpose를 해주는 이유는 shape을 맞추기 위함

- $$a$$ : 계수(coefficient)

### 항등 행렬(Identity Matrix)

$$
\begin{bmatrix}
1 & 0 & 0 \\
0 & 1 & 0 \\
0 & 0 & 1
\end{bmatrix}
$$

- 가운데 값이 1이고 나머지가 0
- 어떤 행렬과 곱해져도 그 행렬이 나온다.
- 행렬 x 역행렬 = 항등행렬
- 직사각행렬은 안된다.
