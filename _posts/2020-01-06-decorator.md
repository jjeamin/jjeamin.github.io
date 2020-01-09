---
layout: post
title:  "딥러닝 학습시키는 시간을 활용해서 Python 알아보기 - 2"
summary: "Python Decorator"
date:   2020-01-06 16:00 -0400
categories: python
---


# Decorator

가끔 프로젝트나 오픈 소스를 보면 함수 위에 `@`가 특정한 이름과 함께 붙어 있는 것을 볼 수 있다. 이런 것을 데코레이터라고 한다. 즉, 꾸며주는 놈이라는 뜻이다.

형태를 보면

```python
@deco
def func():
  ~
  ~
  ~
```

이러한 형태로 되어있다. 일단 왜 쓰는지에 대해서 간단하게 알아보자.

## 왜 쓸까??
코드에서 중복을 제거하기 위해서 사용한다. 반복적인 작업을 해결하기 위해서 사용하는 매크로 같은 역할을 해주는데 모든 함수에 공통적으로 들어가야하는 구문이 있다면 이 것을 줄여주기 위해 사용한다.

## Example
예를 들어보면 **모든 함수** 를 실행시키기 전에 함수의 이름이 어떤 것인지에 대해서 출력을 하고 싶다고 하면

```python
def func1():
    print(func1.__name__)
    print("run code 1")
def func2():
    print(func2.__name__)
    print("run code 2")
def func3():
    print(func3.__name__)
    print("run code 3")

func1()
func2()
func3()
```

대략 이런 식으로 중복이 심하다. 하지만 모든 함수의 이름을 출력하려면 매우 귀찮은 작업이 많이 소요 된다. 그 때 decorator를 사용하는데

```python
def my_deco(func):
    def get_func_name():
        print(func.__name__)
        func()
    return get_func_name

@my_deco
def func1():
    print("run code 1")
@my_deco
def func2():
    print("run code 2")
@my_deco
def func3():
    print("run code 3")

func1()
func2()
func3()
```

이렇게 사용하면 불필요한 작업도 줄이고 가독성이 좋은 코드가 완성된다.

데코레이터에서 내부함수의 이름만 가져와서 리턴해주는 이유는 데코레이터로 꾸며진 함수들을 실행시킨 다음에 실행하기 위해서다. ()를 붙이지 않으면 function object가 리턴되기 때문에 함수를 호출할 때 실행된다. 만약 ()를 붙여서 리턴 한다면 함수를 선언할 때 실행이 될 것이다. 이에 대해서 자세히 알아보고 싶다면 구글에 클로저와 퍼스트 클래스 함수를 찾아보면 된다.


# Reference
- [http://schoolofweb.net/blog/posts](http://schoolofweb.net/blog/posts)
