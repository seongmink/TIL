# 함수형 프로그래밍(Functional Programming)

## 함수형 프로그래밍

**함수란 input을 받아 어떤 역할을 수행해서 값을 반환하는 것을 의미**한다. (자바에서는 메서드의 형태로 함수를 구현)

함수는 역할을 수행한다 느낌으로, 동사에 가깝다고 생각할 수 있다. 이름을 지을때도 메소드는 동사의 형태(e.g. process, drive)로 짓지만, 객체는 명사의 형태로 이름을 짓는다.(e.g. User, Car)

문제 해결에 있어 프로그래밍을 하는데, 주어지는 문제가 명사의 형태로 추상화가 쉬운 경우가 있지만 동사의 형태로 추상화가 쉬운 경우가 있다.

우린 OOP(Object-Oriented Programming)를 배웠기 때문에 모든 문제를 객체를 이용해서 추상화를 하려고 한다.

| 명령형 프로그래밍(Imperative Programming)   | 선언형 프로그래밍(Declarative Programming)   |
| ------------------------------------------- | -------------------------------------------- |
| Object-Oriented Programming                 | Functional Programming                       |
| 문제 해결 : 어떻게 해야 하는가?(How to do?) | 문제 해결 : 무엇을 해야 하는가?(What to do?) |

이 차이를 다음과 같은 예를 통해서 비교 해보자.

> 유저 리스트가 주어졌을때, 검증되지 않은 유저들의 이메일을 리스트로 주세요.

- **명령형 프로그래밍**
  1. 이메일을 담을 리스트 선언
  2. 루프
  3. 유저 선언
  4. 검증되지 않았는지 체크
  5. 않았다면 변수 이메일 추출
  6. 이메일 리스트에 넣기
- **선언형 프로그래밍**
  1. 유저리스트에서
     1. 검증되지 않은 유저만 골라내서
     2. 이메일을 추출해서
  2. 리스트로 받기



## 1급 시민

함수형 프로그래밍은 동사적인 방법으로 문제를 접근을 하고, 이를 위해서 함수의 역할이 증대된다. 따라서 함수는 **1급 시민**이 되어야 하는데 1급 시민이란 무엇일까?

**1급 시민의 조건**

- 함수/메서드의 매개변수(parameter)로서 전달할 수 있는가
- 함수/메서드의 반환값(return)이 될 수 있는가
- 변수에 담을 수 있는가

위 세가지 조건을 만족을 하면 1급 시민이라고 한다. 

> e.g. Object가 1급 시민인가?

Object는 매개변수로서 전달할 수 있고 Object를 리턴할 수 있으며 당연히 변수를 선언해서 Object에 담을 수 있다. 따라서 Object는 1급 시민이라고 할 수 있다.

> e.g. 함수/메서드가 1급 시민인가?

함수는 매개변수로 함수를 지정할 수 없다. 또, 변수를 선언하고 그 변수에다가 함수를 호출한 값이 아니라, 함수 자체를 지정한다면? 그렇다면 그 변수는 무슨 타입이어야 하는가? 너무 애매하다.

그럼, 함수가 1급 시민이 되기 위해서 어떻게 해야할까? 함수를 Object의 형태로 나타낸다면 1급 시민이 되지 않을까?



## 함수형 프로그래밍이 가지는 장점

- 역할에 충실한 코드
  1. 가독성이 좋은 코드
  2. 유지/보수에 용이
  3. 버그로부터 안전
  4. 확장성에 용이
- 패러다임의 전환
  1. Stream, Optional, ... 등등
  2. 다양한 활용 가능성
