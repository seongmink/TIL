# 함수형 프로그래밍 응용(Application of Functional Programming)

## Scope, Closure, Curry

**Scope**는 변수에 접근할 수 있는 범위이다. 함수 안에 함수가 있을 때 내부 함수에서 외부 함수에 있는 변수에 접근이 가능하며(lexical scope) 그 반대는 불가능하다.

```java
public static Supplier<String> getStringSupplier() {
    String hello = "hello";
    Supplier<String> supplier = () -> {
        String world = "world";
        return hello + world;
    };
    
    return supplier;
}
```

내부 함수가 존재하는 한 내부 함수가 사용한 외부 함수의 변수들 역시 계속 존재한다. 이렇게 lexical scope를 포함하는 함수를 **closure**라 한다.

이 때, 내부 함수가 사용한 외부 함수의 변수들은 내부 함수 선언 당시로부터 변할 수 없기 때문에 final로 선언되지 않더라도 암묵적으로 final로 취급된다.

위 개념을 응용한 것이 **curry**이다. 여러 개의 매개변수를 받는 함수를 중첩된 여러 개의 함수로 쪼개어 매개 변수를 한 번에 받지 않고 여러 단계에 걸쳐 나눠 받을 수 있게 하는 기술이다.

```java
BiFunction<Integer, Integer, Integer> add = (x, y) -> x + y;
=>
Function<Integer, Function<Integer, Integer>> add = x -> y -> x + y;
```

어떤 값을 Function에 지정해놓고 싶다면 다음과 같이 사용할 수 있다.

```java
Function<Integer, Function<Integer, Integer>> curriedAdd = x -> y -> x + y;

Function<Integer, Integer> addThree = curriedAdd.apply(3);
int result = addThree.apply(10);
System.out.println(result);
```

3을 addThree라는 Function에 담았다. curriedAdd에 두 인자를 바로 인자를 넘기지 않았으며, 3이라는 값을 사용하고자 할 때 호출하여 쓸 수 있는 것이다.



## Lazy Evaluation

말 그대로 계산을 게으르게 하는 것이다. 계산 값을 필요로 할 때 까지 미루다가 필요하면 계산을 진행한다. 만약 계산 값이 필요하지 않다면 계산을 아예 하지 않는 최적화를 할 수 있으며, 의도적으로 실행 순서를 뒤로 미룰 수 있다.

다음과 같은 메소드가 존재한다고 가정하자.

```java
public boolean returnTrue() {
    System.out.println("Returning true");
    return true;
}

public boolean returnFalse() {
    System.out.println("Returning false");
    return false;
}

public boolean or(boolean x, boolean y) {
    return x || y;
}
```

```java
if (or(returnTrue(), returnFalse())) {
    System.out.println("true");
}
```

위 실행 결과는 어떻게 될까?  returnTrue()가 호출되고, returnFalse() 호출 후, or()를 호출한다 만약 뒤에 있는 returnFalse() 메소드가 cost가 큰 메소드이면, 필요 없는 연산을 수행했기 때문에 낭비가 일어난 것이다.

반면 다음과 같은 메소드가 존재한다면 어떨까?

```java
public boolean lazyOr(Supplier<Boolean> x, Supplier<Boolean> y) {
    return x.get() || y.get();
}
```

```java
if (lazyOr(() -> returnTrue(), () -> returnFalse())) {
    System.out.println("true");
}
```

이 경우에는 returnTrue()만 호출되고 returnFalse()는 호출되지 않는다. 그 이유는 lazyOr 메소드에서 return시에 Supplier의 get()을 하여 이미 값이 true임을 알고 있기 때문에 뒤의 연산이 필요하지 않는 것이다.

스트림에서도 동일하게 lazy evaluation이 일어난다. 중간 처리를 하더라도 종결 처리 전까지 연산을 하지 않기 때문이다.

```java
Stream<Integer> integerStream = Stream.of(3, -2, 5, 8, -3, 10)
                .filter(x -> x > 0)
                .peek(x -> System.out.println("peeking " + x))
                .filter(x -> x % 2 == 0);

System.out.println("Before collect");

List<Integer> integers = integerStream.collect(Collectors.toList());

System.out.println(integers);
```

peek()을 통해 현재 숫자를 출력 후, "Before collect"을 출력할 것 같지만 위 코드를 실행하면 "Before collect"를 출력하고 peeking 을 출력하게 된다. 종결 처리인 collect가 "Before collect"보다 아래에 있기 때문이다.



## Function Composition

여러 개의 함수를 합쳐 하나의 새로운 함수를 만드는 것을 의미한다.

```java
<V> Function<V, R> compose(Function<? super V, ? extends T> before)
<V> Function<T, V> andThen(Function<? super R, ? extends V> after)
```

- compose : 파라미터로 들어온 함수를 먼저 실행하고, 그 다음 자기 자신을 실행한다.

- andThen : 자기를 먼저 실행하고, 파라미터로 들어온 함수를 실행한다.

compose를 사용하면 써있는 순서가 실행의 반대가 되기 때문에, 헷갈려서 보통 andThen을 사용한다고 한다.



