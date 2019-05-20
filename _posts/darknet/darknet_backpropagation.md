# BACKPROPAGATION

신경망이 학습되는 가장 중요한 이유이며 가중치 값을 어떻게 업데이트 하는지에 대한 해답이다. 딥러닝을 하다보면 항상 중요하고 딥러닝의 핵심중의 하나라는 `BACKPROPAGATION`을 보았을 것이다. 어떻게 이렇게 잘되는지 알아봐야겠다. 역전파의 제일 중심이 되는 것은 output에서 시작해서 미분의 연쇄법칙을 이용해 업데이트 하는 것이다. 딥러닝이 값을 학습해 가는 과정은 손실함수를 줄이는 것이다. 손실 함수는 실제값과 예측값의 차이라고 생각하면 쉬울 것 같다. 그리고 gradient descent를 사용해 손실을 줄여 업데이트 한다.

## GRADIENT DESCENT

함수에서 기울기를 계산하면 f(x)에서 x가 얼만큼 업데이트 하였을 때 f(x)의 값이 얼만큼 변화하는지에 대한 벡터가 나온다. 이 벡터가 f(x)의 값이 증가하는 방향을 나타내기 때문에 기울기를 줄여 가면서 업데이트를 해줄수 있는 것이다. 즉, 변화량을 역전파하여서 업데이트 해주는 것이다.

## 미분 연쇄 법칙

편미분을 이용해야 한다. 직접 계산하는 부분은 [여기](https://gomguard.tistory.com/182)에 설명이 잘되어있는것 같다.

# 참조
- [http://jaejunyoo.blogspot.com/2017/01/backpropagation.html](http://jaejunyoo.blogspot.com/2017/01/backpropagation.html)