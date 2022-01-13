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



## BiConsumer

BiFunction에서 봤듯이 BiConsumer도 두 개의 파라미터를 가진다.

```java
@FunctionalInterface
public interface BiConsumer<T, U> {
    void accept(T t, U u);
}
```

T타입과 U타입의 두 개의 파라미터를 input으로 받아 하나의 abstract 메소드를 가지는 것을 볼 수 있다. 또한, consumer 이기 때문에 아무것도 리턴할 것이 없다.

processing 과정에서 사용할 index를 input으로 받아 처리하는 함수형 인터페이스를 만들어보자.

```java
BiConsumer<Integer, Double> myDoubleProcessor = (Integer index, Double input) -> {
    System.out.println("Processing " + input + " at index " + index);
};
```

타입을 유추할 수 있고, 한 줄 밖에 없기 때문에 다음과 같이 더 단순하게 변경할 수 있다.

```java
BiConsumer<Integer, Double> myDoubleProcessor = (index, input) -> 
    System.out.println("Processing " + input + " at index " + index);
```

추가로, Consumer를 넘길 helper 메소드를 생성해보자.

```java
public static <T> void process(List<T> inputs, BiConsumer<Integer, T> processor) {
        for (int i = 0; i < inputs.size(); i++) {
            processor.accept(i, inputs.get(i));
        }
    }
```

BiConsumer의 첫 번째 인자는 인덱스로 사용하기 때문에 Integer 타입으로 받고, 나머지는 제네릭 타입으로 받도록 하자. 이를 사용하기 위해서 임의의 inputs 값을 만들어 인자로 넘겨서 실행해보면 값과 함께 인덱스도 함께 사용하는 것을 확인할 수 있다.

```java
List<Double> inputs = Arrays.asList(1.1, 2.2, 3.3);
process(inputs, myDoubleProcessor);
```



## Predicate

Predicate 인터페이스는 test라는 하나의 abstract 메소드를 가진다. test 메소드는 T라는 하나의 input을 가지고 boolean을 리턴하는 함수이다. 

```java
@FunctionalInterface
public interface Predicate<T> {
    boolean test(T t);
}
```

> 양수인지 음수인지 판별하는 함수를 구현하라.

```java
Predicate<Integer> isPositive = (Integer x) -> {
    return x > 0;
};
```

마찬가지로, input 타입을 유추할 수 있으므로 Integer 생략, input이 하나이기 때문에 괄호 생략, 바로 리턴하기 때문에 중괄호 생략으로 아래와 같이 단순하게 표현할 수 있다.

```java
Predicate<Integer> isPositive = x -> x > 0;
```

> 제네릭 타입인 list와 Predicate를 input으로 받아 Predicate를 통과한 list의 값들만 뽑아, 새로운 list를 만들어 리턴하는 filter 함수를 만들어라.

```java
public static <T> List<T> filter(List<T> inputs, Predicate<T> condition) {
    List<T> output = new ArrayList<>();
    for (T input : inputs) {
        if (condition.test(input)) {
            output.add(input);
        }
    }
    return output;
}
```

위 함수를 테스트 하기 위해 더미 데이터를 만들어보자.

```java
List<Integer> inputs = Arrays.asList(10, -5, 4, -2, 0);
```

여기서, 테스트를 하기 위해 아래와 같이 작성해서 실행하면 양수인 값들만 출력하는 것을 확인할 수 있다.

```java
System.out.println("Positive numbers: " + filter(inputs, isPositive));
```

또한, Predicate 인터페이스 안에 `negate()`, `or()`, `and()`같은 default 메소드가 있는데, 이 또한 값을 판별해주는 메소드들이다. 이 메소드들을 활용해보자.

1. `negate`메소드는  Predicate의 반대 조건으로 동작한다. 아래와 같이 구현하게 되면, x > 0이 아닌 값을 출력하게 된다.

```java
System.out.println("Non-positive numbers: " + filter(inputs, isPositive.negate()));
```

2. Predicate 조건을 0 초과인 값에서 0 이상인 값들로 변경하고 싶다면 어떻게 할까? `or` 메소드를 활용하면 된다. 아래와 같이 새로운 Predicate를 or 조건으로 두고 사용하면 0 이상인 값을 출력할 수 있다.

```java
System.out.println("Non-positive numbers: " + filter(inputs, isPositive.or(x -> x == 0)));
```

3. 마지막으로, `and` 메소드를 활용해보자. 양수이면서 2의 배수인 값을 조건으로 두고싶다면 다음과 같이 구현하면 된다.

```java
System.out.println("Positive even numbers:" + filter(inputs, isPositive.and(x -> x % 2 == 0)));
```

이처럼 filter 메소드를 하나 만들었을 뿐인데, 다양한 방법으로 활용할 수 있다.



## Comparator

Comparator는 java.util.function 안에 존재하는 인터페이스가 아니라 java.util 안에 위치하고 있다.

```java
@FunctionalInterface
public interface Comparator<T> {
    int compare(T o1, T o2);
}
```

- 음수면 o1 < o2
- 0이면 o1 = o2
- 양수면 o1 > o2

Comparator의 비교 대상으로 사용할 User라는 Object를 만들어보자.

```java
public class User {

    private int id;
    private String name;

    public User(int id, String name) {
        this.id = id;
        this.name = name;
    }

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                '}';
    }
}
```

그리고 다음과 같이 테스트 할 더미데이터를 만들어보자. 3개의 객체를 리스트에 담았다.

```java
List<User> users = new ArrayList<>();
users.add(new User(3, "Alice"));
users.add(new User(1, "Charlie"));
users.add(new User(5, "Bob"));
```

이 상태에서 출력을 하게 되면 users에 add 된 순서대로 출력이 되는데, 원하는 순서대로 출력을 하도록 구현해보자.

정렬을 하기 위해 `Collections.sort()`를 사용할 건데, 이 메소드를 확인해보면 인자로 list와 Comparator를 받을 수 있게 되어있다. (List 안에 존재하는 sort()를 사용해도 되며, 역시 인자로 Comparator를 받도록 되어있다.)

> 유저의 id 순서대로 출력하도록 정렬하는 Comparator를 구현하라.

```java
Comparator<User> idComparator = (User u1, User u2) -> {
    return u1.getId() - u2.getId();
};
```

u1의 id가 더 작을 경우에는 음수, 같으면 0을, 더 크다면 양수를 리턴하게 된다. 따라서 id가 더 작을 수록 먼저 출력된다.

여기서 더 단순하게 Comparator를 바꿔보자. User의 타입을 유추할 수 있기 때문에 User 생략, 바로 return 하기 때문에 중괄호 생략하여 다음과 같이 표현할 수 있다.

```java
Comparator<User> idComparator = (u1, u2) -> u1.getId() - u2.getId();
```

> 유저의 name 순서대로 출력하도록 정렬하는 Comparator를 구현하라.

이번에는 Comparator를 바로 인자로 넣어보자. name은 int가 아니라 String이기 때문에 값을 비교하기 위해서 `compareTo()`를 사용한다. 알파벳이 앞서면 음수를, 같으면 0을, 뒤로 가면 양수를 리턴한다. (사전 순)

```java
Collections.sort(users, (u1, u2) -> u1.getName().compareTo(u2.getName()));
```

