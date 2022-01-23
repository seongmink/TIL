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

