---
layout: post
title:  "딥러닝 학습시키는 시간을 활용해서 Python 알아보기 - 3"
summary: "Python Magic Method"
date:   2020-01-06 16:00 -0400
categories: python
---

매직메소드를 알아보자.

# Magic Method

파이썬으로 코드를 작성할 때 클래스를 사용하면 한번 쯤은 `__init__`으로 초기화 하는 것을 해보았을 것이다. 이름 양 옆에 `__`가 붙어있는 함수를 매직 메소드라고 부른다.

매직 메소드는 오버로딩 즉, 함수를 덮어씌울수 있다. 예시를 바로 보자면 여러가지 연산이 가능한 새로운 클래스를 만들어보자

```python
class calc(int):
    def __add__(self, num):
        return '{} + {} = {}'.format(self.real, num.real, self.real + num.real)
    def __sub__(self, num):
        return '{} - {} = {}'.format(self.real, num.real, self.real - num.real)
    def __mul__(self, num):
        return '{} x {} = {}'.format(self.real, num.real, self.real * num.real)
    def __truediv__(self, num):
        return '{} / {} = {}'.format(self.real, num.real, self.real / num.real)
```

매직메소드 만들기

```python
calc_num = calc(5)

print(calc_num + 6)
print(calc_num - 6)
print(calc_num * 6)
print(calc_num / 6)
```

calc class를 int를 상속 받아서 초기화 되므로 class에 int값을 넣어준다.

```
5 + 6 = 11
5 - 6 = -1
5 x 6 = 30
5 / 6 = 0.8333333333333334
```

위와 같이 int형의 magicmethod를 덮어씌워서 내가 실질적으로 연산할 때 결과 뿐 아니라 연산과정을 프린트하게 하였다.

간단하게 말하면 기존에 사용하던 것을 덮어씌워서 마법처럼 내가하고싶은대로 만들 수 있다.

종류에는 여러가지가 있는데 이것저것 많이 해볼 수 있지만 직접 다 찾아보기는 어렵고 [여기](https://ziwon.dev/post/python_magic_methods/)에 파이썬 문서를 번역한 자료를 올려놓아 주셨다. 개념만 알아두고 사용할 때 찾아보자


# Reference
- [http://schoolofweb.net/blog/posts/%ED%8C%8C%EC%9D%B4%EC%8D%AC-oop-part-6-%EB%A7%A4%EC%A7%81-%EB%A9%94%EC%86%8C%EB%93%9C-magic-method/](http://schoolofweb.net/blog/posts/%ED%8C%8C%EC%9D%B4%EC%8D%AC-oop-part-6-%EB%A7%A4%EC%A7%81-%EB%A9%94%EC%86%8C%EB%93%9C-magic-method/)

- [https://ziwon.dev/post/python_magic_methods/](https://ziwon.dev/post/python_magic_methods/)
