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

> randomSupplier와 int count를 파라미터로 받고 count 횟수 만큼 randomSupplier를 호출하여 출력하는 함수를 만들어라

###### printRandomDoubles / count 횟수 만큼 randomSupplier를 호출하는 함수

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

