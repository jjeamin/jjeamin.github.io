# weight initialization

weight를 초기화하는 여러 방법에 대해서 알아봐야 할것 같다. darknet에서는 weight를 초기화 할 때 `scale` 에 `random_nomal`을 곱한 값으로 사용하였다. 이해가 잘 되지않고 weight를 어떻게 초기화해야 학습이 잘될지 알아봐야겠다.

## 종류
- `Zero initialization`
- `Small random number`
- `LeCun Initialization`
- `Xavier Initialization`
- `ReLU Initialization(He Initialization)`

## 확률분포
- `Uniform distribution` : low와 high 값을 잡아주면 확률분포를 생성할 수 있다.
- `Gaussian distribution` : mean과 std 값을 잡아주면 확률분포를 생성할 수 있다. ( = normal,standard)

## Zero Initialization
`Zero Initialization`은 모든 weight를 0으로 초기화 하는 경우를 말한다. 적절한 데이터 정규화를 통해 절반이 양수이고 절반이 음수일 것이라고 가정하는 것이 타당하다. 이렇게 생각한다면 모든 값을 0으로 초기화 하는게 바람직하다고 생각하게 된다. 그러나 실제로 0으로 weight를 초기화 하게 된다면 모든 전파하는 값이 동일할 것이고 역전파에서도 동일하게 이루어질 것이다. 결국 제대로 학습하기 어려울 것이다.

## Small random number
weight를 무작위의 작은 숫자로 초기화해서 대칭적으로 0이 되는 대칭성을 없애보자는 것이 일반적이다. 그러나 잘작동하지는 않는다 왜냐하면 매우 작은 weight를 갖는 신경망은 역전파를 진행하는 동안 아주 작은 gradient를 계산한다. 그래서 gradient signal이 크게 감소할 수 있어서 문제가 발생할 수 있다.

## Lecun Initialization
relu가 나오기전에 lecun이 제안한 방법

## Xavier Initialization(sigmoid)
relu가 등장하고 glorot이 제안한 방법이다. input과 output의 수에 기반해서 초기화의 scale을 정한다.

- Uniform

```
x = sqrt(분산/(in+out))
```

- normal

```
sqrt(2./(in + out))

np.random.randn(in,out) / sqrt(in)
```

## He Initialization(relu)
Xavier와 유사하지만 output의 수를 고려하지 않는다. ReLU가 0 이하의 신호를 제거하기 떄문에 분산을 두배 주어서 분산을 유지한다는 의미에서 시작했다.

- Uniform

```
범위 = sqrt(분산/in)
[-범위,범위]
```

- Normal

```
center가 0이고 stddev = sqrt(2/in)인 정규분포

np.random.randn(in,out) / sqrt(in/2)
```
