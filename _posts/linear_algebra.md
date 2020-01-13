---
layout: post
title:  "Linear Algebra"
summary: "그냥 훑어보기 위해 적어본 선형 대수학 + PCA, LDA"
date:   2020-01-11 16:00 -0400
categories: math
---

가끔 기억 안날때 보려고 매우 간략하게 적었습니다. 자세한 내용을 알고 싶으시면 [https://www.edwith.org/linearalgebra4ai/joinLectures/14072](https://www.edwith.org/linearalgebra4ai/joinLectures/14072) 이 곳 수업에서 알아보시는 것을 추천드립니다.

## 벡터

- vector = 크기 + 방향
- **순서가 정해져 있다.**
- one-dimension
- lowercase
- 표현 : $$x = [1,2,3] \in \mathbb{R}^3$$ (3차원 벡터)

## 행렬
- row vector, column vertor
- 행렬곱 : $$AB \neq BA$$
- uppercase

---

## linear equation

$$a_1 x_1 + a_2 x_2 + \cdots + a_n x_n = b$$

$$a^T x = b$$ 위에 식과 같은식인데 transpose를 해주는 이유는 shape을 맞추기 위함

- $$a$$ : 계수(coefficient)

---

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

---
## linear combination
`span` : 벡터 v1, v2의 선형 조합으로 만들어지는 space

$$
\begin{bmatrix}
60 \\
65 \\
55
 \end{bmatrix} x_1 + \begin{bmatrix}
5.5 \\
5.0 \\
6.0 \end{bmatrix} x_2 + \begin{bmatrix}
1 \\
0 \\
1
\end{bmatrix} x_3 = \begin{bmatrix}
66 \\
74 \\
78
\end{bmatrix}
$$

$$a_1 x_1 + a_2 x_2 + a_3 x_3 = b$$

재료 벡터 $$a_1, a_2, a_3$$의 span안에 b가 존재한다면 값이 존재한다.

---

$$
\begin{bmatrix}
1 \\
1 \\
1 \end{bmatrix}\begin{bmatrix}
1 & 2 & 3
\end{bmatrix} = \begin{bmatrix}
1 & 2 & 3 \\
1 & 2 & 3 \\
1 & 2 & 3
\end{bmatrix}
$$

반대로 진행하면??

$$
\begin{bmatrix}
1 & 2 & 3 \\
1 & 2 & 3 \\
1 & 2 & 3
\end{bmatrix} = \begin{bmatrix}
1 \\
1 \\
1 \end{bmatrix}\begin{bmatrix}
1 & 2 & 3
\end{bmatrix}
$$

만약 3x3 matrix가 일정한 값이 아니라면 위와 같이 분해하는건 불가능하다. 하지만 근사하게는 만들어 줄 수 있을 것이다.


---

## 선형 독립, 선형 종속

$$
\begin{bmatrix}
1 \\
0 \\
0
 \end{bmatrix} x_1 + \begin{bmatrix}
0 \\
1 \\
0 \end{bmatrix} x_2 + \begin{bmatrix}
0 \\
0 \\
1
\end{bmatrix} x_3 = \begin{bmatrix}
0 \\
0 \\
0
\end{bmatrix}
$$

이 경우 x1, x2, x3가 나오는 경우의 수는 셋다 0인 경우의 수 밖에 없다. 그렇다면 이것이 선형 독립(linear independent)

$$
\begin{bmatrix}
1 \\
0 \\
1
 \end{bmatrix} x_1 + \begin{bmatrix}
1 \\
0 \\
0 \end{bmatrix} x_2 + \begin{bmatrix}
0 \\
0 \\
1
\end{bmatrix} x_3 = \begin{bmatrix}
0 \\
0 \\
0
\end{bmatrix}
$$

이 경우 x1, x2, x3가 나오는 경우의 수 무수히 많다. 그렇다면 이것이 선형 종속(linear dependent)

---

# Reference
- [https://www.edwith.org/linearalgebra4ai/joinLectures/14072](https://www.edwith.org/linearalgebra4ai/joinLectures/14072)
