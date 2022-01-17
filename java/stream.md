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

