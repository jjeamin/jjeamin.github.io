# 활성화 함수란?

*퍼셉트론과의 차이점*

딥러닝에서는 계층간의 전달에서 활성화 함수(비선형 함수)를 통과시킨 후 전달한다. 어떤 값을 적절하게 처리해서 활성화 시킨다는 것으로 해석하면 될 것같다. 그런데 왜 활성화 함수를 비선형 함수로 사용하는지도 의문이다. 활성화 함수를 비선형 함수로 사용하는 이유는 **딥러닝에서 계층이 많을 수 있는 이유** 와 같다. 단순히 활성화 함수 없이 사용 될 경우 `y = ...f(f(f(x)))` 와 `y = nx`가 같은 식으로 표현되므로 계층이 많아야 되는 이유가 없다. 그래서 비선형성을 추가시켜 모든 계층이 의미있게 사용될 수 있도록 한다.

---

# ACTIVATION

`/src/activations.c`

## get_activation

```c
ACTIVATION get_activation(char *s)
{
    if (strcmp(s, "logistic")==0) return LOGISTIC;
    if (strcmp(s, "loggy")==0) return LOGGY;
    if (strcmp(s, "relu")==0) return RELU;
    if (strcmp(s, "elu")==0) return ELU;
    if (strcmp(s, "selu")==0) return SELU;
    if (strcmp(s, "relie")==0) return RELIE;
    if (strcmp(s, "plse")==0) return PLSE;
    if (strcmp(s, "hardtan")==0) return HARDTAN;
    if (strcmp(s, "lhtan")==0) return LHTAN;
    if (strcmp(s, "linear")==0) return LINEAR;
    if (strcmp(s, "ramp")==0) return RAMP;
    if (strcmp(s, "leaky")==0) return LEAKY;
    if (strcmp(s, "tanh")==0) return TANH;
    if (strcmp(s, "stair")==0) return STAIR;
    fprintf(stderr, "Couldn't find activation function %s, going with ReLU\n", s);
    return RELU;
}
```

ACTIVATION에 매핑 시켜준다

## activate_array

```c
void activate_array(float *x, const int n, const ACTIVATION a)
{
    int i;
    for(i = 0; i < n; ++i){
        x[i] = activate(x[i], a);
    }
}
```

활성화 함수를 실행해주자

## activate

```c
float activate(float x, ACTIVATION a)
{
    switch(a){
        case LINEAR:
            return linear_activate(x);
        case LOGISTIC:
            return logistic_activate(x);
        case LOGGY:
            return loggy_activate(x);
        case RELU:
            return relu_activate(x);
        case ELU:
            return elu_activate(x);
        case SELU:
            return selu_activate(x);
        case RELIE:
            return relie_activate(x);
        case RAMP:
            return ramp_activate(x);
        case LEAKY:
            return leaky_activate(x);
        case TANH:
            return tanh_activate(x);
        case PLSE:
            return plse_activate(x);
        case STAIR:
            return stair_activate(x);
        case HARDTAN:
            return hardtan_activate(x);
        case LHTAN:
            return lhtan_activate(x);
    }
    return 0;
}
```

활성화 함수가 엄청나게 많다.. 그래프를 그려보면서 알아보도록 하자

`static 변수`
- 전역 변수에 가깝지만 어디서든 접근할 수 없다.
- 지역 변수 안에서만 접근가능하다.

`inline`
- 함수를 호출하지 않고 바로 실행시켜준다.


## linear

```c
static inline float linear_activate(float x){return x;}
```

단순한 선형 활성화 함수를 사용해서 값을 그대로 사용한다.



![linear](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/darknet/linear.PNG)



## logistic

```c
static inline float logistic_activate(float x){return 1./(1. + exp(-x));}
```

`sigmoid`라고 생각하면 된다. 값을 0~1사이로 수렴시킨다.



![sigmoid](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/darknet/sigmoid.PNG)



*단점*
- 결과값이 0과 1부분에 몰려있게 되서 미분값이 0에 수렴하기 때문에 `Gradient Vanishing` 현상이 발생한다.  
- 중간값이 0이 아니라서 학습이 느려질수 있다.


## loggy

```c
static inline float loggy_activate(float x){return 2./(1. + exp(-x)) - 1;}
```
sigmoid와 모양은 유사하고 -1 ~ 1에 수렴시킨다. 중심값이 0이다.



![loggy](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/darknet/loggy.PNG)



## relu

```c
static inline float relu_activate(float x){return x*(x>0);}
```
Rectified linear unit의 약자이며 **가장 많이 사용되는 활성화함수** 다. 음수면 신호를 없애고 양수 신호만 사용한다. 값의 몰림 현상이 없어지고 계산이 매우 효율적이다.



![relu](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/darknet/relu.PNG)



*단점*
- 중심값이 0이 아니다.
- 음수값은 영향이 없다. -> 모든 값을 의미있게 사용하지 않는다.

## elu

```c
static inline float elu_activate(float x){return (x >= 0)*x + (x < 0)*(exp(x)-1);}
```

Exponential linear unit의 약자이며 relu의 특성을 가지고 있다. 음수일때 기울기가 매우 작고 양수일때는 그대로 사용한다. 음수를 없애지않는다.



![elu](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/darknet/elu.PNG)



## selu



```c
static inline float selu_activate(float x){return (x >= 0)*1.0507*x + (x < 0)*1.0507*1.6732*(exp(x)-1);}
```


![selu](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/darknet/selu.PNG)



## relie

```c
static inline float relie_activate(float x){return (x>0) ? x : .01*x;}
```


![relie](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/darknet/reile.PNG)



## ramp

```c
static inline float ramp_activate(float x){return x*(x>0)+.1*x;}
```



![ramp](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/darknet/ramp.PNG)



## leaky relu

relu의 특성을 가지고있고 음수일때 기울기가 0.01이다.



```c
static inline float leaky_activate(float x){return (x>0) ? x : .1*x;}
```



![leaky](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/darknet/leaky.PNG)



## tanh

```c
static inline float tanh_activate(float x){return (exp(2*x)-1)/(exp(2*x)+1);}
```
sigmoid와 유사하지만 중심이 0이다.



![tanh](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/darknet/tanh.PNG)



*단점*
- `Gradient Vanishing` 현상이 발생

## plse

```c
static inline float plse_activate(float x)
{
    if(x < -4) return .01 * (x + 4);
    if(x > 4)  return .01 * (x - 4) + 1;
    return .125*x + .5;
}
```



![plse](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/darknet/plse.PNG)



## stair

```c
static inline float stair_activate(float x)
{
    int n = floor(x);
    if (n%2 == 0) return floor(x/2.);
    else return (x - n) + floor(x/2.);
}
```

계단식 함수다.




![stair](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/darknet/stair.PNG)



## hardtan

```
static inline float hardtan_activate(float x)
{
    if (x < -1) return -1;
    if (x > 1) return 1;
    return x;
}
```



![hardtan](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/darknet/hardtan.PNG)



## lhtan

```
static inline float lhtan_activate(float x)
{
    if(x < 0) return .001*x;
    if(x > 1) return .001*(x-1) + 1;
    return x;
}
```



![lhtan](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/darknet/lhtan.PNG)



0.001을 곱해서 잘 안보이지만 매우 살짝 기울어져 있다.



**활성화 함수들은 거의다 조금씩 조금씩 변형한게 많기 때문에 인기가 많았던 것 위주로 봤다. 게다가 활성화 함수에서 Gradient를 계산 하는게 중요한 것 같다. Gradient는 역전파에서 자세히 살펴보도록 해야할 것 같다.**

---

# GRADIENT

backpropagation로 값들을 업데이트 해야하기 때문에 미분 값(기울기)을 이용한다.

## gradient_array

```
void gradient_array(const float *x, const int n, const ACTIVATION a, float *delta)
{
    int i;
    for(i = 0; i < n; ++i){
        delta[i] *= gradient(x[i], a);
    }
}
```

## gradient

```c
float gradient(float x, ACTIVATION a)
{
    switch(a){
        case LINEAR:
            return linear_gradient(x);
        case LOGISTIC:
            return logistic_gradient(x);
        case LOGGY:
            return loggy_gradient(x);
        case RELU:
            return relu_gradient(x);
        case ELU:
            return elu_gradient(x);
        case SELU:
            return selu_gradient(x);
        case RELIE:
            return relie_gradient(x);
        case RAMP:
            return ramp_gradient(x);
        case LEAKY:
            return leaky_gradient(x);
        case TANH:
            return tanh_gradient(x);
        case PLSE:
            return plse_gradient(x);
        case STAIR:
            return stair_gradient(x);
        case HARDTAN:
            return hardtan_gradient(x);
        case LHTAN:
            return lhtan_gradient(x);
    }
    return 0;
}

```

## linear

```c
static inline float linear_gradient(float x){return 1;}
```

## logistic

```
static inline float logistic_gradient(float x){return (1-x)*x;}
```

## loggy

```
static inline float loggy_gradient(float x)
{
    float y = (x+1.)/2.;
    return 2*(1-y)*y;
}
```

## relu

```c
static inline float relu_gradient(float x){return (x>0);}
```

## elu

```
static inline float elu_gradient(float x){return (x >= 0) + (x < 0)*(x + 1);}
```

## selu

```c
static inline float selu_gradient(float x){return (x >= 0)*1.0507 + (x < 0)*(x + 1.0507*1.6732);}
```

## relie

```c
static inline float relie_gradient(float x){return (x>0) ? 1 : .01;}
```

## ramp

```c
static inline float ramp_gradient(float x){return (x>0)+.1;}
```

## leaky relu

```c
static inline float leaky_gradient(float x){return (x>0) ? 1 : .1;}
```

## tanh

```c
static inline float tanh_gradient(float x){return 1-x*x;}
```

## plse

```c
static inline float plse_gradient(float x){return (x < 0 || x > 1) ? .01 : .125;}
```

## stair

```c
static inline float stair_gradient(float x)
{
    if (floor(x) == x) return 0;
    return 1;
}
```

## lhtan

```c
static inline float lhtan_gradient(float x)
{
    if(x > 0 && x < 1) return 1;
    return .001;
}

```

## hardtan

```c
static inline float hardtan_gradient(float x)
{
    if (x > -1 && x < 1) return 1;
    return 0;
}
```


# 참조

- [https://medium.com/@prateekvishnu/activation-functions-in-neural-networks-bf5c542d5fec](https://medium.com/@prateekvishnu/activation-functions-in-neural-networks-bf5c542d5fec)

- [https://ratsgo.github.io/deep%20learning/2017/04/22/NNtricks/](https://ratsgo.github.io/deep%20learning/2017/04/22/NNtricks/)
