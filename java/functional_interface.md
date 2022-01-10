# 함수형 인터페이스(Functional Interface)

## Supplier

supply는 공급하다라는 뜻이 있다. 따라서, Supplier는 공급하는 인터페이스라고 생각하면 된다.

```java
@FunctionalInterface
public interface Supplier<T> {
    T get();
}
```

이 Supplier를 예시로 코드를 작성해보자.

```java
Supplier<String> myStringSupplier = () -> {
    return "hello world";
};
System.out.println(myStringSupplier.get());
```

항상 `hello world` 라는 문자열을 리턴한다. 이를 사용하고자 하면 `get()` 메소드를 호출하여 사용하면 된다. 여기서 함수의 내용이 다른 문장이 없고 바로 리턴하기 때문에 아래와 같이 더 단순하게 만들 수 있다. 

```java
Supplier<String> myStringSupplier = () -> "hello world";
```

> 호출 할 때 마다 랜덤한 double을 리턴하는 Supplier를 만들어라.

랜덤한 double 을 리턴하기 위해서 `Math.random()` 메소드를 사용했다.

```java
Supplier<Double> myRandomDoubleSupplier = () -> Math.random();
System.out.println(myRandomDoubleSupplier.get());
System.out.println(myRandomDoubleSupplier.get());
System.out.println(myRandomDoubleSupplier.get());
```

실행을 해보면 새로운 double을 생성하는 것을 확인할 수 있다.

또한, 이 함수가 1등 시민이 되었기 때문에 이를 파라미터로 넘길 수 있다.

> randomSupplier와 int count를 파라미터로 받고 count 횟수 만큼 randomSupplier를 호출하여 출력하는 printRandomDobules 함수를 만들어라

```java
public static void printRandomDoubles(Supplier<Double> randomSupplier, int count) {
    for(int i = 0; i < count; i++) {
        System.out.println(randomSupplier.get());
    }
}
```

위와 같이 만들고 이를 호출하려면 다음과 같이 사용하면 된다.

```java
printRandomDoubles(myRandomDoubleSupplier, 5);
```

확장한다면 random number를 다른 방법으로 만드는 함수를 만들어서 printRandomDoubles에 supplier를 해줄 수 있겠다. 이런 방식을 통해 유연하게 다른 종류의 supplier를 공급함으로써, 랜덤 함수를 만드는 방법은 전혀 관여하지 않도록 구현할 수 있다.



## Consumer

consumer와 supplier는 정 반대 개념이라고 생각하면 된다. supplier는 input 파라미터는 없고 무언가를 리턴하는 함수형 인터페이스이지만, consumer는 반대로 아무것도 리턴하지 않는 함수형 인터페이스이다. 

```java
@FunctionalInterface
public interface Consumer<T> {
    void accept(T t);
}
```

무언가를 파라미터로 받지만, 안에서 어떤 동작을 진행하고 리턴을 하지 않는 함수를 나타낼 때 Consumer를 사용한다.

> String을 받고 출력만 하는 Consumer를 만들어라.

```java
Consumer<String> myStringConsumer = (String str) -> {
    System.out.println(str);
};
myStringConsumer.accept("hello");
myStringConsumer.accept("world");
```

`accept()` 메소드를 호출하여 사용하면 된다. 또한, 리턴이 아니어도 한 줄 밖에 없는 경우에는 중괄호를 생략할 수 있다. consumer의 같은 경우는 리턴이 아니어도 가능하다. 따라서 다음과 같이 단순하게 표현할 수 있다.

```java
Consumer<String> myStringConsumer = str -> System.out.println(str);
```

> Integer Consumer와 Integer 리스트를 받아 process하는 함수를 만들어라.

```java
public static void process(List<Integer> inputs, Consumer<Integer> processor) {
    for (Integer input : inputs) {
        processor.accept(input);
    }
}
```

이렇게 작성하면 어떤 방식으로 구현된 Consumer든지 함수에서는 구현 방식에 관여하지 않는다. 단순히 input에 대해 호출해주기만 하고, 로직같은 경우는 다양하게 만들어서 함수에 넘길 수 있다.

```java
Consumer<Integer> myIntegerProcessor = x -> System.out.println("Processing integer " + x);
process(integerInputs, myIntegerProcessor);

Consumer<Integer> myDifferentIntegerProcessor = x -> System.out.println("Processing integer in different way " + x);
process(integerInputs, myDifferentIntegerProcessor);
```

> 위에서 만든 함수를 어떤 타입으로든 받을 수 있도록 제네릭 타입으로 변경해라.

```java
public static <T> void process(List<T> inputs, Consumer<T> processor) {
    for (T input : inputs) {
        processor.accept(input);
    }
}
```

이렇게 작성하면 Integer만 받을 수 있던 함수에서 어떤 타입이든 받을 수 있게 되었다.

```java
List<Double> doubleInputs = Arrays.asList(1.1, 2.2, 3.3);

Consumer<Double> myDoubleProcessor = x -> System.out.println("Processing double " + x);
process(doubleInputs, myDoubleProcessor);
```



