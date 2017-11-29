---
layout: post
title: Functional Programming Basic Concepts
date: 2017-02-05 09:36:55
tags: FP, scala
categories: [FP]
---

# Functional Programming
## Basic Concepts
### Pure Functions(순수 함수)
Pure function은 Input에의해 output(반환값)이 결정되는 함수를 의미한다. 이 과정에서 어떤 부작용(side effect)도 발생하지 않아야 한다.
이는 Math.cos(x)와 같이 동일한 x에 대해 항상 같은 값을 반환하는 수학적 함수와 동일한 개념이다. 그리고 주어진 x의 값을 절대 바꾸지 않으며, 로그 파일이 기록하거나, 네트워크 요청을 보내는 등은 하지 않는다.

이런 함수가 반환값의 계산 이외의 행위를 한다면, 그 함수는 impure(순수하지 않은) function이다. 따라서 특정 함수에서 impure function을 호출한다면 해당 함수 또한 impure function이다.

위에 주어진 함수의 경우 pure function이기때문에 해당 함수가 반환하는 값으로 대체되어도 무방하다. 즉, ```Math.cos(Math::PI)```는  -1로 대체해버릴 수 있는 것이다. 이런 속성을 referential transparency라고 한다.

pure function의 일반적인 규칙은 아래와 같다.
* 주어진 input이외의 것으로 결과값이 바뀌어서는 안된다.
* 주어진 input의 값을 절대 바꿀 수 없다.
* 함수 밖, 함수가 실행되는 context상에 존재하는 것을 바꿀 수 없다.
* 주어진 input이 동일할 경우, 항상 동일한 결과값을 반환해야 한다.

위에서 사용한 ```Math.cos(Math::PI)```룰 -1로 바꿀 수 있는 것을 볼때, pure function은 값(데이터)로 생각할 수 도 있다. 그리고 pure function들은 일급 객체(first class citizens)이다. 즉, pure function들은 변수처럼 다른 함수에 주어질 수 있고 새로운 기능(함수)를 작성하는데 부분적으로 적용될 수 도 있다.

즉, **함수는 재사용가능하고 조립(? 조합) 가능해야 한다**.

#### Comments
##### referential transparency
참조 투명성, 관계 투명성으로 번역되고 있다. 하지만 원문 자체로 기억하는 것이 좋을 것 같다.
##### Scala Code

```
Math.cos(Math.PI)
```

### Immutable Values(불변 객체, 값)
OOP와 같이 Stateful한 언어들로 동시성을 제어하는 것은 어려우며 다음과 같은 것들을 사용해서 코드를 안전하게 작성해야 한다.
* mutexes
* locks
* semaphores
* access constraints

하지만 **FP에서는 가변(Stateful, mutable)한 것을 허용하지 않는다.** 즉, 객체 혹은 값이 한번 설정되면 절대 바꿀 수 없다.

### Monads


### References
* [functional-programming-pure-functions/](https://www.sitepoint.com/functional-programming-pure-functions/)
* [Functional-Programming-Concepts-Idioms-and-Philosophy/](https://hkupty.github.io/2016/Functional-Programming-Concepts-Idioms-and-Philosophy/)
