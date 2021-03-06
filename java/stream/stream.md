# 스트림(Stream)

## Stream

스트림은 함수형 인터페이스와 함께 Java 8에서 추가되었다. 함수형 인터페이스를 적극 활용하여 데이터를 간편하게 가공할 수 있다.

stream은 시내, 개울이라는 뜻을 가진 단어이다. 자바에서의 스트림이란, 물이 흐르는 것처럼 데이터의 흐름을 만들어주는 것이다. 스트림은 컬렉션 형태로 구성된 데이터를 람다를 이용해 간결하고 직관적으로 프로세스하게 해주며, for와 while 같이 사용하던 기존 loop를 대체할 수 있고 손쉽게 병렬 처리를 할 수 있게 해준다.

스트림을 생성하여 활용하는 방법에 대해 살펴보자.

- Stream 타입의 스트림을 만들어보자. `Stream.of`라는 static method를 사용하여 스트림을 만든다. of 안에 스트림 타입의 원하는 만큼을 넣어줄 수 있다. 이어서, 생성된 스트림을 리스트로 만들어보자. `Collectors.toList()` 를 사용하면 리스트로 변환해준다.

  ```java
  Stream<String> nameStream = Stream.of("Alice", "Bob", "Charlie");
  List<String> names = nameStream.collect(Collectors.toList());
  ```

- 문자열 배열로 된 것들을 Stream으로 변환해보자. 마찬가지로 생성된 스트림을 리스트로 만들어보자.

  ```java
  String[] cityArray = new String[]{"San Jose", "Seoul", "Tokyo"};
  Stream<String> cityStream = Arrays.stream(cityArray);
  List<String> cityList = cityStream.collect(Collectors.toList());
  ```

- Integer타입을 담는 Set이 있다고 가정해보자. 참고로 hashSet을 만들 때 안에 Collection을 넣을 수 있다. Set은 자바 컬렉션의 일종이므로, 스트림을 바로 바꿔주는 메소드가 존재한다. `Collection.steram()` 을 사용하여 스트림으로 변환해보자.

  ```java
  Set<Integer> numberSet = new HashSet<>(Arrays.asList(3, 5, 7));
  Stream<Integer> numberStream = numberSet.stream();
  List<Integer> numberList = numberStream.collect(Collectors.toList());
  ```



## Stream의 구성요소

스트림은 다음과 같이 세 부분으로 구성되어 있다.

- Source (소스) : 컬렉션, 배열 등
- Intermediate Operations (중간 처리) : 0개 이상의 filter, map 등의 중간 처리, 여러가지의 중간 처리를 이어붙이는 것이 가능하다.
- Terminal Operation (종결 처리) : collect, reduce 등



## Filter

무엇인가를 걸러주는 거름종이 같은 필터를 의미한다. 스트림에서 어떤 조건을 만족하는 데이터만 걸러내는데 사용된다. 다음과 같이 Predicate에 true를 반환하는 데이터만 존재하는 stream을 리턴한다.

```java
Stream<T> filter(Predicate<? super T> predicate);
```

> 양수를 걸러내는 필터를 만들어라

```java
Stream<Integer> numberStream = Stream.of(3, -5, 7, 10, -3);
Stream<Integer> filteredNumberStream = numberStream.filter(x -> x > 0);
List<Integer> filteredNumbers = filteredNumberStream.collect(Collectors.toList());
```

다음과 같이 스트림을 of로 생성하고, 바로 filter를 호출하고 collect를 호출해도 된다.

```java
List<Integer> newFilteredNumbers = Stream.of(3, -5, 7, 10, -3)
                .filter(x -> x > 0)
                .collect(Collectors.toList());
```

추가적인 실전에 가까운 예제를 위해 3가지 POJO를 만들어보자.

```java
public class User {

    private int id;
    private String name;
    private String emailAddress;
    private boolean isVerified;
    private List<Integer> friendUserIds;

    // getter, setter, toString 생략
    // setter의 리턴 타입은 User이며, 체이닝을 위해 return this를 넣어준다.
}
```

```java
public class Order {

    private long id;
    private LocalDateTime createdAt;
    private long createdByUserId;
    private OrderStatus status;
    private BigDecimal amount;
    private List<OrderLine> orderLines;

    public enum OrderStatus {
        CREATED,
        IN_PROGRESS,
        ERROR,
        PROCESSED
    }
    
    // getter, setter, toString 생략
    // setter의 리턴 타입은 Order이며, 체이닝을 위해 return this를 넣어준다.
}
```

```java
public class OrderLine {

    private long id;
    private OrderLineType type;
    private long productId;
    private int quantity;
    private BigDecimal amount;

    public enum OrderLineType {
        PURCHASE,
        DISCOUNT
    }

    // getter, setter, toString 생략
}
```

일단 유저들을 생성하고, 리스트에 담자.

```java
User user1 = new User()
                .setId(101)
                .setName("Alice")
                .setVerified(true)
                .setEmailAddress("alice@test.com");

User user2 = new User()
                .setId(102)
                .setName("Bob")
                .setVerified(false)
                .setEmailAddress("bob@test.com");

User user3 = new User()
                .setId(103)
                .setName("Charlie")
                .setVerified(true)
                .setEmailAddress("charlie@test.com");

List<User> users = Arrays.asList(user1, user2, user3);
```

> users 리스트안에서 verified 된 유저들만 걸러내라.

```java
List<User> verifiedUsers = users.stream()
                .filter(User::isVerified)
                .collect(Collectors.toList());
```

> users 리스트 안에서 verified 되지 않은 유저들만 걸러내라.

```java
List<User> unverifiedUsers = users.stream()
                .filter(user -> !user.isVerified())
                .collect(Collectors.toList());
```

추가적인 예제를 위하여 Order를 생성하고 리스트에 담자.

```java
Order order1 = new Order()
                .setId(1001)
                .setStatus(Order.OrderStatus.CREATED)
			    .setCreatedByUserId(101)
                .setCreatedAt(LocalDateTime.now().minusHours(4));

Order order2 = new Order()
                .setId(1002)
                .setStatus(Order.OrderStatus.ERROR)
			    .setCreatedByUserId(103)
                .setCreatedAt(LocalDateTime.now().minusHours(1));

Order order3 = new Order()
                .setId(1003)
                .setStatus(Order.OrderStatus.PROCESSED)
                .setCreatedByUserId(102)
                .setCreatedAt(LocalDateTime.now().minusHours(36));

Order order4 = new Order()
                .setId(1004)
                .setStatus(Order.OrderStatus.ERROR)
                .setCreatedByUserId(104)
                .setCreatedAt(LocalDateTime.now().minusHours(40));

Order order5 = new Order()
                .setId(1005)
                .setStatus(Order.OrderStatus.IN_PROGRESS)
                .setCreatedByUserId(101)
                .setCreatedAt(LocalDateTime.now().minusHours(10));

List<Order> orders = Arrays.asList(order1, order2, order3, order4, order5);
```

> ERROR 상태인 order만 걸러내라.

```java
List<Order> filteredOrders = orders.stream()
                .filter(order -> order.getStatus() == Order.OrderStatus.ERROR)
                .collect(Collectors.toList());
```



## Map

map은 스트림안에 있는 데이터를 변형하는데 사용한다.

```java
<R> Stream<R> map(Function<? super T, ? extends R> mapper);
```

T라는 데이터를 스트림에서 데이터마다 Function을 적용하여 R이라는 타입을 바꿔주고 이 결과를 스트림을 리턴하는 것이다.

> Integer로 된 리스트의 값들을 2배한 리스트를 만들어라.

```java
List<Integer> numberList = Arrays.asList(3, 6, -4);
List<Integer> numberListX2 = numberList.stream()
    			.map(x -> x * 2)
                .collect(Collectors.toList());
```

> 리스트의 값을 출력하는 map을 구현하라.

위에서 생성된 numberList를 재활용하여 구현해보자.

```java
List<String> strList = numberList.stream()
                .map(x -> "Number is " + x)
                .collect(Collectors.toList());
```

> 위 filter에서 사용했던 user 리스트(List\<User> users)를 활용하여, 이메일만 모으는 map을 구현하라.

```java
List<String> emailAddresses = users.stream()
                .map(User::getEmailAddress)
                .collect(Collectors.toList());
```

> 위 filter에서 사용했던 order 리스트(List\<Order> orders)를 활용하여, createdByUserId를 모으는 map을 구현하라.

```java
List<Long> createdByUserIds = orders.stream()
                .map(Order::getCreatedByUserId)
                .collect(Collectors.toList());
```



## Sorted

데이터가 순서대로 정렬된 stream을 리턴해주며, 데이터의 종류에 따라 Comparator가 필요할 수 있다.

```java
Stream<T> sorted();
Stream<T> sorted(Comparator<? super T> comparator);
```

> 임의의 정수를 담은 리스트를 생성하고, 이 리스트를 오름차순으로 정렬하는 새로운 리스트를 생성하라.

```java
List<Integer> numbers = Arrays.asList(3, -5, 7, 4);
List<Integer> sortedNumbers = numbers.stream()
                .sorted()
                .collect(Collectors.toList());
```

> 위 filter에서 사용했던 users 리스트(List\<User> users)를 활용하여, 이름을 오름차순으로 정렬하라. 단, name 값을 수정하여 진행한다. (users1=Paul, users2=David, users3=John 으로 변경)

```java
List<User> sortedUsers = users.stream()
                .sorted((u1, u2) -> u1.getName().compareTo(u2.getName()))
                .collect(Collectors.toList());
```

> 위 filter에서 사용했던 order 리스트(List\<Order> orders)를 활용하여, 가장 먼저 만들어진 order 순으로 정렬하라. (createdAt 기준)

```java
List<Order> sortedOrders = orders.stream()
                .sorted((o1, o2) -> o1.getCreatedAt().compareTo(o2.getCreatedAt()))
                .collect(Collectors.toList());
```



## Distinct

스트림 안에 있는 데이터들 중에서 중복을 제거하여, unique 한 값들만 스트림을 만들어준다. Object의 경우에는 같은지 경우를 비교할 때 equals 비교를 하니 equals override를 하지 않으면 원하지 않은 결과를 받을 수 있으니 유의해야 한다.

```java
Stream<T> distinct();
```

> 정수로 이루어진 리스트에서 중복된 값을 제거하여 새로운 리스트를 만들어라.

```java
List<Integer> numbers = Arrays.asList(3, -5, 4, -5, 2, 3);
List<Integer> distinctNumbers = numbers.stream()
                .distinct()
                .collect(Collectors.toList());
```

> 위 fitler에서 사용했던 order 리스트(List\<Order> orders)를 활용하여, 중복되지 않은 createdByUserId를 오름차순으로 정렬하여 리스트로 만들어라.

```java
List<Long> userIds = orders.stream()
                .map(Order::getCreatedByUserId)
                .distinct()
                .sorted()
                .collect(Collectors.toList());
```



## FlatMap

스트림을 사용하다보면 최종 결과가 Stream 인 경우가 있다. 데이터를 변형할 때, 그 결과가 스트림이 된다면 그 스트림을 쭉 이어서 하나로 만드는 것이 map을 적용하는 것이고 추가로 중첩된 스트림을 납작하게 만드는 flatmap이다. (데이터에 함수를 적용한 후 중첩된 stream을 연결하여 하나의 stream으로 리턴)

아래의 flatMap을 살펴보자.

```java
<R> Stream<R> flatMap(Function<? super T, ? extends Stream<? extends R>> mapper);
```

위 function은 T를 받아 R 타입의 데이터를 나오는 스트림을 반환한다. map을 통하여 위 function에 넘긴다면 R 타입의 스트림들이 나오는 스트림을 하나씩 받는다. 그 안에는 R타입의 스트림이 들어있다. 이것을 납작하게 스트림을 벗기고 R타입의 스트림을 받는 것이아니라 R을 받을 수 있도록 만들어 주는 것이 flatMap이다.

다음과 같은 2차원 문자열 배열 cities가 존재한다고 하자.

```java
String[][] cities = new String[][] {
    {"Seoul", "Busan"},
    {"San Francisco", "New York"},
    {"Madrid", "Barcelona"}
};
```

이 2차원 문자열 배열을 하나의 List로 담고 싶다면 어떻게 해야할까?

```java
Stream<String[]> cityStream = Arrays.stream(cities);
Stream<Stream<String>> cityStreamStream = cityStream.map(x -> Arrays.stream(x));
List<Stream<String>> cityStreamList = cityStreamStream.collect(Collectors.toList());
```

이렇게 변환하고자 했지만, List\<Stream\<String>> 과 같이 원하지 않는 타입이 리턴된다. 이것을 flatMap을 사용하여 해결해보자.

```java
Stream<String[]> cityStream2 = Arrays.stream(cities);
Stream<String> flattenedCityStream = cityStream2.flatMap(x -> Arrays.stream(x));
List<String> flattenedCityList = flattenedCityStream.collect(Collectors.toList());
```

위와 같이 flatMap을 사용하여 원하는 문자열 List로 담을 수 있게 되었다.

추가로, 다음과 같은 Order들이 존재한다고 해보자.

```java
Order order1 = new Order()
                .setId(1001)
                .setOrderLines(Arrays.asList(
                        new OrderLine()
                            .setId(10001)
                            .setType(OrderLine.OrderLineType.PURCHASE)
                            .setAmount(BigDecimal.valueOf(5000)),
                        new OrderLine()
                            .setId(10002)
                            .setType(OrderLine.OrderLineType.PURCHASE)
                            .setAmount(BigDecimal.valueOf(4000))
                ));

Order order2 = new Order()
                .setId(1002)
                .setOrderLines(Arrays.asList(
                        new OrderLine()
                                .setId(10003)
                                .setType(OrderLine.OrderLineType.PURCHASE)
                                .setAmount(BigDecimal.valueOf(2000)),
                        new OrderLine()
                                .setId(10004)
                                .setType(OrderLine.OrderLineType.DISCOUNT)
                                .setAmount(BigDecimal.valueOf(-1000))
                ));

Order order3 = new Order()
                .setId(1003)
                .setOrderLines(Arrays.asList(
                        new OrderLine()
                                .setId(10005)
                                .setType(OrderLine.OrderLineType.PURCHASE)
                                .setAmount(BigDecimal.valueOf(2000))
                ));
```

Order가 여러개가 존재하는데, 하나의 Order로 합쳐야 하는 경우가 존재한다. (사용자가 장바구니에 물건들을 담고 한번에 결제하려고 하는 경우) 최종적으로 List\<OrderLine>을 가지고 싶다고 할때 flatMap을 활용하여 구현해보자.

```java
List<Order> orders = Arrays.asList(order1, order2, order3);
List<OrderLine> mergedOrderLines = orders.stream() // Stream<Order>
                .map(Order::getOrderLines)         // Stream<List<OrderLine>>
//              .map(List::stream)                 // Stream<Stream<OrderLine>> (X)
                .flatMap(List::stream)             // Stream<OrderLine>
                .collect(Collectors.toList());
```



------

스트림의 구성 요소 중 Terminal Operation (종결 처리)에 대해서 알아보자. 종결 처리를 통해 최종 결과물을 도출하는데, 종결 처리의 실행이 진행될 때 중간 처리들도 비로소 실행 된다(Lazy Evaluation)

##### Stream의 종결 처리들

- max / min / count / allMatch / anyMatch / findFirst / findAny / reduce / forEach

##### Collector를 이용한 종결 처리들

- toList / toSet / mapping / reducing / toMap / groupingBy / partitioningBy

## Max / Min / Count

스트림안의 데이터의 최대값/최소값/개수를 뽑아낼 때 사용한다.

```java
Optional<T> max(Comparator<? super T> comparator);
Optional<T> min(Comparator<? super T> comparator);
long count();
```

- max : stream 안의 데이터 중 최대값을 반환. stream이 비어있다면 빈 Optional을 반환
- min : stream 안의 데이터 중 최소값을 반환. stream이 비어있다면 빈 Optional을 반환
- count : stream 안의 데이터의 개수를 반환



## All Match / Any Match

```java
boolean allMatch(Predicate<? super T> predicate);
boolean anyMatch(Predicate<? super T> predicate);
```

- allMatch : stream 안의 모든 데이터가 predicate을 만족하면 true
- anyMatch : steram 안의 데이터 중 하나라도 predicate을 만족하면 true



## Find First / Find Any

```java
Optional<T> findFirst();
Optional<T> findAny();
```

- findFirst : stream 안의 첫번째 데이터를 반환. stream이 비어있다면 빈 Optional을 반환
- findAny : steram 안의 아무 데이터나 리턴. 순서가 중요하지 않고 Parallel Stream을 사용할 때 최적화를 할 수 있다. stream이 비어있다면 빈 Optional을 반환



## Reduce

주어진 함수를 반복 적용해 stream 안의 데이터를 하나의 값으로 합치는 작업이다. max, min, count도 reduce의 일종임.

```java
Optional<T> reduce(BinaryOperator<T> accumulator);
T reduce(T identity, BinaryOperator<T> accumulator);
<U> U reduce(U identity, BiFunction<U, ? super T, U> accumulator, BinaryOperator<U> combiner);
```

- reduce1 : 주어진 accumulator를 이용해 데이터를 합침. stream이 비어있을 경우 빈 Optional을 반환
- reduce2 : 주어진 초기값과 accumulator를 이용. 초기값이 있기 때문에 항상 반환값이 존재
- reduce3 : 합치는 과정에서 타입이 바뀔 경우 사용. Map + reduce로 대체 가능

순차적으로 동작하는 예시를 보자.

```java
List<Integer> numbers = Arrays.asList(1, 4, -2, -5, 3);
```

이러한 리스트가 존재한다고 할 때, 이 값들을 reduce를 사용하여 모두 더해보는 코드를 다음과 같이 작성할 수 있다.

##### reduce1 예시

```java
int min = numbers.stream()
                .reduce((x, y) -> x + y)
                .get()
```

이 경우, 1과 4를 더한 값(5)과 -2를 더한 값(3)과 -5를 더한 값(-2)과 3을 더한 값(1)을 반환하게 된다.

##### reduce2 예시

```java
int product = numbers.stream()
                .reduce(1, (x, y) -> x * y);
```

기본 값이 1로 지정하고 나머지 값들을 곱한 값을 반환하도록 했다. 기본 값이 존재하기 때문에 Optional이 아니며, get() 을 사용하여 값을 가져오지 않는다.

##### reduce3 예시

```java
List<String> numberStrList = Arrays.asList("3", "2", "5", "-4");
```

위와 같은 문자열로 된 숫자가 존재하고 이 숫자들을 더한 값을 얻고 싶을 때,

```java
int sumOfNumberStrList = numberStrList.stream()
                .map(Integer::parseInt)
                .reduce(0, (x, y) -> x + y);
```

이렇게 map을 사용하여 숫자로 변환하고, reduce를 사용하여 연산하면 된다. 하지만 이 경우를 더 간단하게 할 수 있는 방법이 있다. (map + reduce 같은 방법을 더 많이 사용하긴 함)

```java
int sumOfNumberStrList2 = numberStrList.stream()
                .reduce(0, (number, str) -> number + Integer.parseInt(str), (num1, num2) -> num1 + num2);
```



## To Map

stream 안의 데이터를 map의 형태로 반환해주는 Collector이다.

```java
public static <T, K, U> Collector<T, ?, Map<K, U>> toMap(
    Function<? super T, ? extends K> keyMapper, 
    Function<? super T, ? extends U> valueMapper)
```

- keyMapper : 데이터를 map의 key로 변환하는 Function
- valueMapper : 데이터를 map의 value로 변환하는 Function

> 임의의 정수들을 만든 후, 정수들을 key로 만들고 key를 나타내는 문자열을 value로 지정하여 <Integer, String> 타입의 map으로 변환하라.

```java
Map<Integer, String> numberMap = Stream.of(3, 5, -4, 2, 6)
                .collect(Collectors.toMap(x -> x, x -> "Number is " + x));
```

이와 같이 나타낼 수 있으며, 여기서 `x -> x` 로 나타낸 Function을  `Function.identity()` 로 사용해도 무방하다.



## Grouping By

stream 안의 데이터에 classifier를 적용했을 때 결과값이 같은 값 끼리 List로 모아서 Map의 형태로 반환해주는 Collector이다.

```java
public static <T, K> Collector<T, ?, Map<K, List<T>>>> groupingBy(Function<? super T, ? extends K> classifier)
```

예를 들어, stream에 {1, 2, 3, 5, 7, 9, 12 13}이 있을 때 classifier가 `x -> x % 3` 이라면 map은 {0 = [9, 12], 1= 1, 7, 13], 2 = 2[2, 5]} 가 된다.

다음과 같이 두 번째 매개변수로 downstream collector를 넘기는 것도 가능하다. 이 경우 List대신 Collector를 적용시킨 값으로 map의 value가 만들어지며, 자주 쓰이는 것이 mapping / reducing 등의 Collector이다.

```java
public static <T, K, A, D> Collector<T, ?, Map<K, D>> groupingBy(Function<? super T, ? extends K> classifier, Collector<? super T, A, D> downstream)
```



## Partitioning By

groupingBy와 유사하지만 Function 대신 Predicate을 받아 true와 false, 두 key가 존재하는 map을 반환하는 Collector이다.

마찬가지로 downstream collector를 넘겨 List 이외의 형태로 map의 value를 만드는 것이 가능하다.

```java
public static <T> Collector<T, ?, Map<Boolean, List<T>>> partitioningBy(Predicate<? super T> predicate)
public static <T, D, A> Collector<T, ?, Map<Boolean, D>> partitioningBy(Predicate<? super T> predicate, Collector<? super T, A, D> downstream)
```



## For Each

제공된 action을 stream의 각 데이터에 적용해주는 종결 처리 메소드이다.

Java의 iterable 인터페이스에도 forEach가 있기 때문에 stream의 중간 처리가 필요없다면 iterable collection(Set, List 등)에서 바로 쓰는 것도 가능하다.

```java
void forEach(Consumer<? super T> action);
```



## Parrallel Stream

기존 순차 처리(Sequentail)하던 스트림을 병렬 처리(Parallel) 할 수 있도록 변경할 수 있다.

여러개의 스레드를 이용하여 stream의 처리 과정을 병렬화 (parallelize) 한다.

중간 과정은 병렬 처리 되지만 순서가 있는 stream의 경우 종결 처리 했을때의 결과물이 기존 순차적 처리와 일치하도록 종결 처리과정에서 조정된다. 즉 List로 collect한다면 순서가 항상 올바르게 나온다.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3);
Stream<Integer> parallelStream = numbers.parallelStream();
Stream<Integer> parallelSteram2 = numbers.stream().parallel();
```

##### 장점

- 굉장히 간단하게 병렬 처리를 사용할 수 있게 해준다.
- 속도가 비약적으로 빨라질 수 있다.

##### 단점

- 항상 속도가 빨라지는 것은 아니다.
- 공통으로 사용하는 리소스가 있을 경우 잘못된 결과가 나오거나 아예 오류가 날 수도 있다. (deadlock)
- 이를 막기 위해 mutex, semaphore 등 병렬 처리 기술을 이용하면 순차 처리보다 느려질 수도 있다.

