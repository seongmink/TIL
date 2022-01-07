# 람다식(Lambda Expression)

## Function Interface

함수가 1급 시민이 되려면 함수를 Object 형태로 바꿔야 한다. 이를 가능하게 해주는 것이 **Functional Interface**이다.

java.util.function 패키지 안을 보면 많은 function이 존재한다. 그 중 Function을 살펴보자.

```java
@FunctionalInterface
public interface Function<T, R> {
    R apply(T t);
}
```

Function이 interface 타입인데, OOP에서 인터페이스는 설계도와 같고 그 안에 구현되지 않은 메소드들이 있다고 배웠다.

Function 인터페이스 같은 경우는 apply라는 하나의 abstract 메소드를 가지고 있고 T는 apply 메소드의 input 타입이고 R은 apply 메소드의 리턴 타입이다. 따라서, Function 인터페이스가 나타내고자 하는 것은 T라는 input을 받아 R이라는 타입의 값을 리턴하는 함수를 인터페이스 형태로 구현한 것이다.

> e.g. 어떤 Integer를 받고 1을 더해 반환하는 메소드는?

T : Integer, R : Integer

> e.g. 어떤 Integer를 받아 String을 반환하는 메소드는?

T : Integer, R : String

인터페이스를 구현하려면 Object를 만들고 implement를 사용하여 그 안에 구현되지 않은 메소드들을 전부 구현하면 된다.

> 값을 받아 10을 반환하는 메소드를 가지고 있는 Object를 생성하라.

```java
public class Adder implements Function<Integer, Integer> {

    @Override
    public Integer apply(Integer x) {
        return x + 10;
    }
}
```

이렇게 구현을 할 수 있다. 이를 사용하고자 한다면 다음과 같이 사용하면 된다.

```java
Function<Integer, Integer> myAdder = new Adder();
int result = myAdder.apply(5);
```

하지만, 이 경우 Object를 만들어줘야 하기 때문에 번거롭다. 함수를 Object 형태로 만들고 싶을 때마다 새로운 Object를 만들어서 호출해야 하나? 클래스가 너무 많아지지 않나? 같은 의문이 든다. 이 문제를 해결할 수 있는 방법이 바로 **람다**이다.

------

## 람다식

람다식에 앞서 함수의 구성요소에 대해서 다시 살펴보자.

### 함수의 구성 요소

- 함수의 이름
- 반환값의 타입 (return type)
- 매개변수 (parameters)
- 함수의 내용 (body)



자바의 메소드를 생각해보면 메소드를 선언을 할 때, 제일 먼저 메소드의 리턴타입을 쓰고 그 뒤에 메소드의 이름을 쓴다. 그리고 소괄호를 열고 매개변수를 선언해주고 소괄호를 닫고, 중괄호를 열어서 함수의 내용을 넣은 뒤 중괄호를 닫는 형태로 되어있다.

람다는 이름이 없는 함수(Anonymous function)이다. 위에서 Object 형태로 만들었던 것을 람다로 표현하면 다음과 같다.

```java
(Integer x) -> {
    return x + 10;
}
```

매개변수로 Integer를 받고, 함수의 내용이 존재하며 `x + 10` 을 보니 리턴 타입이 Integer라는 것을 알 수 있다. 함수의 구성 요소에서 모든 것이 충족되지만 함수의 이름이 존재하지 않는다. 그래서 람다는 이름이 없는 함수라고도 한다. 또한, 매개변수와 함수의 내용 사이에 `->` 이 있는데, 화살표 표시의 operator가 존재하는 것을 알 수 있다.

람다로 수정한다면 다음과 같이 작성을 할 수 있으며 이 표현식 또한 제대로 동작한다. 위 설명과 마찬가지로 메소드 자체는 이름이 존재하지 않는다.  `myAdder`는 이름 없는 메소드를 담고 있는 상자라고 생각하면 된다.

```java
Function<Integer, Integer> myAdder = (Integer x) -> {
    return x + 10;
};
int result = myAdder.apply(5);
```

여기서 이 람다식을 다음 조건을 만족한다면 더 간단하게 표현할 수 있다.

- 매개변수의 타입이 유추 가능할 경우 타입 생략 가능
- 매개변수가 하나일 경우 괄호 생략 가능
- 바로 리턴하는 경우 중괄호 생략 가능

`Function<Integer, Integer>` 를 보면 input 타입이 Integer라는 것을 알 수 있으므로 생략 가능하다. 또한, input이 x로 하나이기 때문에 괄호도 생략이 가능하다. 다른 문장이 없고 바로 리턴하므로 중괄호 생략이 가능하다.

더 간단하게 표현하자면 다음과 같다.

```java
Function<Integer, Integer> myAdder = x -> x + 10;
int result = myAdder.apply(5);
```

Object 형태로 구현 했던 Function 인터페이스를 클래스 생성 없이 람다식을 통해 더 간단하게 표현할 수 있다는 것을 확인할 수 있다.

