# 메소드 참조(Method Reference)

## Method Reference

새로 메소드를 호출하는 람다식을 만들 필요 없이 해당 메소드 자체를 바로 넘길 수 있는 방법이 있다. 이것을 **메소드 참조**라고 부른다.

메소드 참조는 **기존에 이미 선언되어있는 메소드를 지정하고 싶을 때** 사용하며  `::` 오퍼레이터를 사용한다. 또한, 생략이 많기 때문에 사용할 메소드의 매개변수의 타입과 리턴 타입을 미리 숙지해야 한다.

메소드 참조의 4가지 케이스는 다음과 같다.

- 클래스의 static method를 지정할 때(ClassName::staticMethodName)

- 선언된 객체의 instance method를 지정할 때(objectName::instanceMethodName)
- 객체의 instance method를 지정할 때(ClassName::instanceMethodName)
- 클래스의 constructor를 지정할 때(ClassName::new)

이 4가지 케이스에 대해서 자세히 살펴보자. 주의할 점은, 메소드를 호출하는 것이 아니라 지정하는 것이기 때문에 뒤에 괄호()를 쓰지 않는다

1. 클래스의 static method를 지정할 때 (ClassName::staticMethodName)

   Integer.parseInt() 메소드를 예를 들면, parseInt() 메소드는 어떤 String을 Integer로 바꾸고 싶을 때 사용하는 메소드이다. Integer안에 static 메소드로 정의되어 있기 때문에 Integer.parseInt() 로 사용하고 String 하나를 받아 Integer를 리턴한다. 그 말은 input 하나를 받아 리턴하는 것이므로 Functional Interface를 사용하여 나타낼 수 있다.

   메소드 레퍼런스로 바꾸면 다음과 같다. 

   ```java
   Function<String, Integer> str2int = Integer::parseInt;
   int five = str2int.apply("5");
   ```

2. 선언된 객체의 instance method를 지정할 때 (objectName::instanceMethodName)

   String.equlas() 메소드를 예를 들면, String object 안에 equals라는 메소드가 존재한다. 하나의 String 객체를 생성하고, 이 객체와 어떤 문자열이 동일한지 판별하려면 객체이름.equals() 과 같이 사용한다. equals() 메소드는 String을 하나 받아서 boolean을 리턴하기 때문에 Predicate로 만들 수 있다.

   메소드 레퍼런스로 바꾸면 다음과 같다.

   ```java
   String str = "hello";
   Predicate<String> equlasToHello = str::equals;
   boolean helloEqualsWorld = equalsHello.test("world");
   ```

3. 해당 클래스의 인스턴스를 매개변수(parameter)로 넘겨 메소드를 실행해주는 함수 (ClassName::instanceMethodName)

   첫 번째로, String.length() 메소드를 예를 들어보자. 이는 String의 길이를 리턴하는 메소드이다. 이것의 input은 String이 될 것이고, 리턴은 길이기 때문에 Integer가 될 것이다. 

   두 번째로, 두 개의 문자를 받아서 비교를 하려면 어떻게 해야할까? String과 String을 받아서 equals를 호출하여 boolean을 리턴해야하니, BiPredicate를 쓰면 해결된다. 혹은 BiFunction<String, String, Boolean>으로도 해결할 수 있겠다.

   메소드 레퍼런스로 바꾸면 다음과 같다.

   ```java
   Function<String, Integer> strLength = String::length;
   int length = strLength.apply("hello world!");
   
   BiPredicate<String, String> strEquals = String::equals;
   boolean result = strEquals.test("hello", "world");
   ```

4. 클래스의 constructor를 지정할 때 (ClassName::new)

   생성자로 객체를 생성할 때도 마찬가지로 메소드 참조를 통해 생성할 수 있다. 생성자의 인자가 Integer, String로 되어 있는 클래스의 생성자가 존재한다면, BiFunction<Integer, String, 클래스이름> 과 같이 사용하면 된다.

   ```java
   User user = new User(1, "Alice");
   BiFunction<Integer, String, User> userCreator = User::new;
   User charlie = userCreator.apply(3, "Charlie");
   ```

   OOP의 원리를 결합하여 사용하는예제를 좀 더 살펴보자.

   먼저, abstract 클래스인 Car를 만들어보자. (미 구현된 메소드들을 받을 수 있음) protected 키워드를 사용하여 상속받은 클래스만 사용 가능하게 하자. 또한, drive라는 abstract 메소드를 만들어 다양한 방법으로 구현할 수 있게 하자.

   ```java
   public abstract class Car {
       protected String name;
       protected String brand;
   
       public Car(String name, String brand) {
           this.name = name;
           this.brand = brand;
       }
   
       public abstract void drive();
   }
   ```

   이를 상속 받는 3개의 차의 종류를 생성해보자. (Sedan, Ven, SUV)

   ```java
   public class Sedan extends Car {
   
       public Sedan(String name, String brand) {
           super(name, brand);
       }
   
       public void drive() {
           System.out.println("Driving a Sedan : " + name + " from " + brand);
       }
   }
   ```

   ```java
   public class Suv extends Car {
   
       public Suv(String name, String brand) {
           super(name, brand);
       }
   
       public void drive() {
           System.out.println("Driving an SUV : " + name + " from " + brand);
       }
   }
   ```

   ```java
   public class Ven extends Car {
   
       public Ven(String name, String brand) {
           super(name, brand);
       }
   
       public void drive() {
           System.out.println("Driving a Ven : " + name + " from " + brand);
       }
   }
   
   ```

   다음과 같이 2차원 배열로 input이 들어온다고 가정해보자. 2차원 배열 안에 들어오는 값은 차례로 차의 종류와, 차의 이름과, 차의 브랜드이다.

   ```java
   String[][] inputs = new String[][]{
       {"sedan", "Sonata", "Hyundai"},
       {"van", "Sienna", "Toyota"},
       {"sedan", "Model S", "Tesla"},
       {"suv", "Sorento", "KIA"}
   };
   ```

   이를 바탕으로 Car 리스트를 받아서 리턴하고 싶다면 기존에는 하나씩 if else를 사용하여 확인하여 각각 다른 생성자를 호출하는 수 밖에 없고, 새로운 차 종류가 추가될 때마다 점점 길어질 것이다.

   먼저, 자동으로 Constructor를 꺼내주는 Map을 생성해보자.

   ```java
   Map<String, BiFunction<String, String, Car>> carTypeToConstructorMap = new HashMap<>();
   carTypeToConstructorMap.put("Sedan", Sedan::new);
   carTypeToConstructorMap.put("Suv", Suv::new);
   carTypeToConstructorMap.put("Van", Van::new);
   ```

   이를 담는 cars라는 이름의 리스트를 생성하고, 반복문을 돌아 map에서 생성자를 찾아 생성 후 리스트 안에 차례로 넣도록 구현해보고, 이 리스트에 객체들이 정상적으로 추가가 되었는지 확인해보자.

   ```java
   List<Car> cars = new ArrayList<>();
   for (int i = 0; i < inputs.length; i++) {
       String[] input = inputs[i];
       String type = input[0];
       String name = input[1];
       String brand = input[2];
       
       cars.add(carTypeToConstructorMap.get(type).apply(name, brand));
   }
   
   for (Car car : cars) {
       car.drive();
   }
   ```

   이렇게 구현하면 새로운 차 종류가 추가되더라도 Map에 하나의 Entry만 추가하면 되기 때문에 확장성에서 강점이 있다.

