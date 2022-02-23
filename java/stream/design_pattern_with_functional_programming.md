# 함수형 프로그래밍을 이용한 디자인 패턴(Design Pattern With Functional Programming)

## 디자인 패턴

디자인 패턴은 반복해서 등장하는 프로그래밍 문제들에 대한 해법들을 패턴화 해놓은 것이다. 패턴들을 숙지해놓으면 비슷한 문제가 생겼을 때 패턴들이 이정표가 되어준다. 

- **생성 패턴** (Creational Patterns) : 오브젝트의 생성에 관련된 패턴
- **구조 패턴** (Structural Patterns) : 상속을 이용해 클래스/오브젝트를 조합하여 더 발전된 구조로 만드는 패턴
- **행동 패턴** (Behavioral Patterns) : 필요한 작업을 여러 객체에 분배하여 객체간 결합도를 줄이게 해주는 패턴

대부분의 디자인 패턴들은 구현에 많은 인터페이스/클래스/메소드를 필요로 한다. 이를 함수형 프로그래밍을 이용해 몇몇 패턴을 좀 더 간단하게 구현할 수 있다.



## 빌더 패턴(Builder Pattern)

- 대표적인 생성 패턴이다.
- 객체의 생성에 대한 로직과 표현에 대한 로직을 분리해준다.
- 객체의 생성 과정을 유연하게 해준다.
- 객체의 생성 과정을 정의하고 싶거나 필드가 많아 constructor가 복잡해질 때 유용하다.

빌더 패턴을 위해서는 `Builder`라는 inner class를 정의한다.

```java
public class User {

    private int id;
    private String name;
    private String emailAddress;
    private boolean isVerified;
    private List<Integer> friendUserIds;
    private LocalDateTime createdAt;

    public User(Builder builder) {
        this.id = builder.id;
        this.name = builder.name;
        this.emailAddress = builder.emailAddress;
        this.isVerified = builder.isVerified;
        this.createdAt = builder.createdAt;
        this.friendUserIds = builder.friendUserIds;
    }

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public Optional<String> getEmailAddress() {
        return Optional.ofNullable(emailAddress);
    }

    public boolean isVerified() {
        return isVerified;
    }

    public List<Integer> getFriendUserIds() {
        return friendUserIds;
    }

    public LocalDateTime getCreatedAt() {
        return createdAt;
    }

    public static Builder builder(int id, String name) {
        return new Builder(id, name);
    }

    public static class Builder {
        private int id;
        private String name;
        public String emailAddress;
        public boolean isVerified;
        public List<Integer> friendUserIds = new ArrayList<>();
        public LocalDateTime createdAt;

        private Builder(int id, String name) {
            this.id = id;
            this.name = name;
        }

        public Builder withEmailAddress(String emailAddress) {
            this.emailAddress = emailAddress;
            return this;
        }

        public Builder withVerified(boolean isVerified) {
            this.isVerified = isVerified;
            return this;
        }

        public Builder withCreatedAt(LocalDateTime createdAt) {
            this.createdAt = createdAt;
            return this;
        }

        public Builder withFriendUserIds(List<Integer> friendUserIds) {
            this.friendUserIds = friendUserIds;
            return this;
        }

        public User build() {
            return new User(this);
        }
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", emailAddress='" + emailAddress + '\'' +
                ", isVerified=" + isVerified +
                ", friendUserIds=" + friendUserIds +
                '}';
    }
}
```

하지만 이 경우, 필드 값이 많아지면 많아질수록 Builder도 마찬가지로 많아질 수 밖에 없다. 이 점을 함수형 프로그래밍을 통해 변경해보자.

`withXXX`들을 전부 삭제하고 하나의 with을 만들자. consumer를 받아 accept를 호출, this를 리턴하여 동일한 결과를 나타낼 수 있다.

```java
public Builder with(Consumer<Builder> consumer) {
    consumer.accept(this);
    return this;
}
```

##### 기존 코드

```java
User user = User.builder(1, "Alice")
                .withEmailAddress("alice@test.com")
                .withVerified(true)
                .build();
```

##### 함수형 프로그래밍 적용

```java
User user = User.builder(1, "Alice")
    			.with(builder -> {
        			builder.emailAddress = "alice@test.com";
        			builder.isVerified = true;
    			}).build();
```



## 데코레이터 패턴(Decorator Pattern)

- 구조 패턴의 하나이다.
- 용도에 따라 객체에 기능을 계속 추가(decorate)할 수 있게 해준다.

예제를 위해 String 타입의 가격을 담고 있는 Price 클래스를 생성한다. 가격을 나타내기 위해 숫자형으로 선언하는게 맞지만, 어떻게 적용 되는지 확인하기 위해 문자열로 진행한다.

```java
public class Price {
    private final String price;

    public Price(String price) {
        this.price = price;
    }

    public String getPrice() {
        return price;
    }
}
```

PriceProcessor들의 뼈대가 될 함수형 인터페이스를 만든다.

```java
@FunctionalInterface
public interface PriceProcessor {
    Price process(Price price); // Price를 받아서 process를 하고 Price를 리턴함

    default PriceProcessor andThen(PriceProcessor next) {
        return price -> next.process(process(price));
    }
}
```

andThen이 호출되면 새로운 PriceProcessor를 리턴한다. 구현되지 않은 메소드가 하나밖에 없기 때문에 `@FunctionalInterface`임을 이용하여 람다를 사용하여 위와 같이 구현할 수 있다. 기존 price를 받아와서 자신 먼저 process를 해주고, next로 들어온 process를 하여 새로운 PriceProcessor를 만들어서 리턴해준다.

##### BasicPriceProcessor

단순히 아무것도 process를 진행하지 않고, Price를 리턴하는 priceProcess 이다.

```java
public class BasicPriceProcessor implements PriceProcessor {

    @Override
    public Price process(Price price) {
        return price;
    }
}
```

##### DiscountPriceProcessor

기존 price에 discount 처리했다는 내용을 반환하는 priceProcess를 만든다.

```java
public class DiscountPriceProcessor implements PriceProcessor {

    @Override
    public Price process(Price price) {
        return new Price(price.getPrice() + ", then applied discount");
    }
}
```

##### TaxPriceProcessor

기존 price에 tax 처리했다는 내용을 반환하는 priceProcess를 만든다.

```java
public class TaxPriceProcessor implements PriceProcessor {

    @Override
    public Price process(Price price) {
        return new Price(price.getPrice() + ", then applied tax");
    }
}
```

Price와 세 개의 priceProcessor로 적용해보자.

```java
// init ----------------------
Price unprocessedPrice = new Price("Original Price");

PriceProcessor basicPriceProcessor = new BasicPriceProcessor();
PriceProcessor discountPriceProcessor = new DiscountPriceProcessor();
PriceProcessor taxPriceProcessor = new TaxPriceProcessor();
// ---------------------------
// 1) decorate 적용
PriceProcessor decoratedPriceProcessor = basicPriceProcessor
    .andThen(discountPriceProcessor)
    .andThen(taxPriceProcessor);

Price processedPrice = decoratedPriceProcessor.process(unprocessedPrice);
System.out.println(processedPrice.getPrice());
// => Original Price, then applied discount, then applied tax
// ---------------------------
// 2) 람다 적용
PriceProcessor decoratedPriceProcessor2 = basicPriceProcessor
    .andThen(taxPriceProcessor)
    .andThen(price -> new Price(price.getPrice() + ", then apply another procedure"));

Price processedPrice2 = decoratedPriceProcessor2.process(unprocessedPrice);

System.out.println(processedPrice2.getPrice());
// => Original Price, then applied tax, then apply another procedure
```



## 전략 패턴(Strategy Pattern)

- 대표적인 행동 패턴이다.
- 런타임에 어떤 전략(알고리즘)을 사용할 지 선택할 수 있게 해준다.
- 전략들을 캡슐화 하여 간단하게 교체할 수 있게 해준다.

전략을 `이메일을 어떻게 만드는가`로 잡고 이를 구현해보자.

먼저, User를 받아 이메일을 만들어서 리턴을 해주는 인터페이스를 만든다.

```java
public interface EmailProvider {
    String getEmail(User user);
}
```

전략을 사용할 EmailSender를 만든다. EmailProvider를 가지고 있고, 실시간으로 전략을 바꿀 수 있도록 setter를 하나 만들고, 이메일을 보내는 sendEmail을 추가하자.

```java
public class EmailSender {
    private EmailProvider emailProvider;

    public EmailSender setEmailProvider(EmailProvider emailProvider) {
        this.emailProvider = emailProvider;
        return this;
    }

    public void sendEmail(User user) {
        String email = emailProvider.getEmail(user);
        System.out.println(email);
    }
}
```

EmailProvider들의 뼈대가 될 인터페이스를 만든다.

```java
public interface EmailProvider {
    String getEmail(User user);
}
```

##### VerifyYourEmailAddressEmailProvider

이메일을 인증하지 않은 사용자에게 인증을 하라는 내용을 가진 getEmail를 만든다.

```java
public class VerifyYourEmailAddressEmailProvider implements EmailProvider {

    @Override
    public String getEmail(User user) {
        return "'Verify Your Email Address' email for " + user.getName();
    }
}
```

##### MakeMoreFriendsEmailProvider

친구의 수가 적은 사용자에게 친구를 더 만들으라는 내용을 가진 getEmail을 만든다.

```java
public class MakeMoreFriendsEmailProvider implements EmailProvider {

    @Override
    public String getEmail(User user) {
        return "'Make More Friends' email for " + user.getName();
    }
}
```

위 두 개의 Provider로 예제를 만들어보자.

```java
// init ----------------------
User user1 = User.builder(1, "Alice")
    .with(builder -> {
        builder.emailAddress = "alice@test.com";
        builder.isVerified = false;
        builder.friendUserIds = Arrays.asList(201, 202, 203, 204, 211, 212, 213, 214);
    }).build();

User user2 = User.builder(2, "Bob")
    .with(builder -> {
        builder.emailAddress = "bob@test.com";
        builder.isVerified = true;
        builder.friendUserIds = Arrays.asList(212, 213, 214);
    }).build();

User user3 = User.builder(3, "Charlie")
    .with(builder -> {
        builder.emailAddress = "charlie@test.com";
        builder.isVerified = true;
        builder.friendUserIds = Arrays.asList(201, 202, 203, 204, 211, 212);
    }).build();

List<User> users = Arrays.asList(user1, user2, user3);

EmailSender emailSender = new EmailSender();
EmailProvider verifyYourEmailAddressEmailProvider = new VerifyYourEmailAddressEmailProvider();
EmailProvider makeMoreFriendsEmailProvider = new MakeMoreFriendsEmailProvider();
// ---------------------------
// 1) strategy 적용 (전략을 인증으로 선택 후 인증하지 않은 사용자에게 이메일을 보냄)
emailSender.setEmailProvider(verifyYourEmailAddressEmailProvider);
users.stream()
    .filter(user -> !user.isVerified())
    .forEach(emailSender::sendEmail);
// => Sending 'Verify Your Email Address' email for Alice
// ---------------------------
// 2) strategy 적용 (인증된 사용자이면서 친구의 수가 5명 이하인 사용자에게 친구를 더 만들으라는 이메일을 보냄)
emailSender.setEmailProvider(makeMoreFriendsEmailProvider);
users.stream()
    .filter(User::isVerified)
    .filter(user -> user.getFriendUserIds().size() <= 5)
    .forEach(emailSender::sendEmail);
// => Sending 'Make More Friends' email for Bob
// ---------------------------
// 3) 람다 적용
emailSender.setEmailProvider(user -> "'Play With Friends' email for " + user.getName());
users.stream()
    .filter(User::isVerified)
    .filter(user -> user.getFriendUserIds().size() > 5)
    .forEach(emailSender::sendEmail);
// => Sending 'Play With Friends' email for Charlie
```



## 템플릿 메소드 패턴(Template Method Pattern)

- 또 하나의 대표적인 행동 패턴이다.
- 상위 클래스는 알고리즘의 뼈대만을 정의하고 알고리즘의 각 단계는 하위 클래스에게 정의를 위임하는 패턴이다.
- 알고리즘의 구조를 변경하지 않고 세부 단계를 유연하게 변경할 수 있게 해준다.

먼저 알고리즘의 뼈대를 담당할 abstarct class를 생성한다. 

**AbstractUserService**

미 구현된 메소드들은 abstract를 구현하는 클래스들이 구현하게 된다. 검증하는 과정과 DB에 쓰는 과정도 하위 클래스에게 맡긴다.

```java
public abstract class AbstractUserService {
    protected abstract boolean validateUser(User user);

    protected abstract void writeToDB(User user);

    public void createUser(User user) {
        if (validateUser(user)) {
            writeToDB(user);
        } else {
            System.out.println("Cannot create users");
        }
    }
}
```

##### UserService

`AbstractUserService`를 상속받으며 유저의 이름과 이메일이 존재하면 검증이 성공되었다고 하자.

```java
public class UserService extends AbstractUserService {

    @Override
    protected boolean validateUser(User user) {
        System.out.println("Validating user " + user.getName());
        return user.getName() != null && user.getEmailAddress().isPresent();
    }

    @Override
    protected void writeToDB(User user) {
        System.out.println("Writing user " + user.getName() + " to DB");
    }
}
```

##### InternalUserService

`AbstractUserService`를 상속받으며 어떤 경우이든 성공하도록 validateUser를 구현했다.

```java
public class InternalUserService extends AbstractUserService {

    @Override
    protected boolean validateUser(User user) {
        System.out.println("Validating internal user " + user.getName());
        return true;
    }

    @Override
    protected void writeToDB(User user) {
        System.out.println("Writing user " + user.getName() + " to internal DB");
    }
}
```

위 두 클래스를 활용하는 예제를 만들어보자.

```java
// init ----------------------
User alice = User.builder(1, "Alice")
    .with(builder -> {
        builder.emailAddress = "alice@test.com";
        builder.isVerified = false;
        builder.friendUserIds = Arrays.asList(201, 202, 203, 204, 211, 212, 213, 214);
    }).build();

UserService userService = new UserService();
InternalUserService internalUserService = new InternalUserService();
// ---------------------------
// 1) userService 적용
userService.createUser(alice);
// => Validating user Alice
//    Writing user Alice to DB
// 2) internalUserService 적용
internalUserService.createUser(alice);
// => Validating internal user Alice
//    Writing user Alice to internal DB
```

뼈대는 같고 구현하는 내용은 다르기 때문에 처리하는 과정이 다르다.

템플릿을 매번 새로운 클래스를 만들지 않고 유연하게 하기 위해 함수형 프로그래밍을 통해 구현해보자. 새로운 템플릿을 만들고 abstract method 대신 함수형 인터페이스를 가지도록 만들자.

```java
public class UserServiceInFunctionalWay {
    private final Predicate<User> validateUser;
    private final Consumer<User> writeToDB;

    public UserServiceInFunctionalWay(Predicate<User> validateUser, Consumer<User> writeToDB) {
        this.validateUser = validateUser;
        this.writeToDB = writeToDB;
    }

    public void createUser(User user) {
        if (validateUser.test(user)) {
            writeToDB.accept(user);
        } else {
            System.out.println("Cannot create user");
        }
    }
}
```

validateUser와 writeToDB라는 함수형 인터페이스를 갖도록 만들었다. 이 함수형 인터페이스들은 Object가 만들어질때 생성자에서 받아올 수 있도록 한다. createUser도 함수형 인터페이스를 넘겨주는 값에 따라 변경할 수 있도록 수정하자.

```java
UserServiceInFunctionalWay userServiceInFunctionalWay = new UserServiceInFunctionalWay(
    user -> {
        System.out.println("Validating user " + user.getName());
        return user.getName() != null && user.getEmailAddress().isPresent();
    },
    user -> {
        System.out.println("Writing user " + user.getName() + " to DB");
    });

userServiceInFunctionalWay.createUser(alice);
// => Validating user Alice
//    Writing user Alice to DB
```



## 책임 연쇄 패턴(Chain of Responsibility Pattern)

- 행동 패턴의 하나이다.
- 명령과 명령을 각각의 방법으로 처리할 수 있는 처리 객체들이 있을 때,
  - 처리 객체들을 체인으로 엮는다.
  - 명령을 처리 객체들이 체인의 앞에서부터 하나씩 처리해보도록 한다.
  - 각 처리 객체는 자신이 처리할 수 없을 때 체인의 다음 처리 객체로 명령(책임)을 넘긴다.
  - 체인의 끝에 다다르면 처리가 끝난다.
- 새로운 처리 객체를 추가하는 것으로 매우 간단히 처리 방법을 더할 수 있다.

orderProcess하며 각 단계를 나타내는 `OrderProcessStep`을 만들자.

```java
public class OrderProcessStep {
    private final Consumer<Order> processOrder;
    private OrderProcessStep next;

    public OrderProcessStep(Consumer<Order> processOrder) {
        this.processOrder = processOrder;
    }

    public OrderProcessStep setNext(OrderProcessStep next) {
        if (this.next == null) {
            this.next = next;
        } else {
            this.next.setNext(next);
        }
        return this;
    }

    public void process(Order order) {
        processOrder.accept(order);
        Optional.ofNullable(next)
                .ifPresent(nextStep -> nextStep.process(order));
    }
}
```

여기서 next는 다음 명령을 가질 대상을 지목하기 위해 필요한데, linkedList를 생각하면 된다. setNext는 현재 대상의 다음을 가리키게 된다. `process()`는 consumer를 실행해주고, 다음 next가 존재한다면 실행할 수 있게 해준다.

이를 사용하는 예제를 만들어보자.

```java
// init ----------------------
// 1) CREATED 상태일 때 실행하며, 상태를 IN_PROGRESS로 변경한다.
OrderProcessStep initializeStep = new OrderProcessStep(order -> {
   if (order.getStatus() == Order.OrderStatus.CREATED) {
       System.out.println("Start processing order " + order.getId());
       order.setStatus(Order.OrderStatus.IN_PROGRESS);
   }
});
// 2) IN_PROGRESS 상태일 때 실행하며, 주문 값을 더해준다.
OrderProcessStep setOrderAmountStep = new OrderProcessStep(order -> {
    if (order.getStatus() == Order.OrderStatus.IN_PROGRESS) {
        System.out.println("Setting amount of order " + order.getId());
        order.setAmount(order.getOrderLines().stream()
                .map(OrderLine::getAmount)
                .reduce(BigDecimal.ZERO, BigDecimal::add));
    }
});
// 3) IN_PROGRESS 상태일 때 실행하며, 더한 값이 유효한 숫자인지 확인하는 작업을 거친다. (0 이하인 경우를 잘못된 상태라고 가정) 만약 잘못되었다면 상태를 ERROR로 변경한다.
OrderProcessStep verifyOrderStep = new OrderProcessStep(order -> {
    if (order.getStatus() == Order.OrderStatus.IN_PROGRESS) {
        System.out.println("Verifying order " + order.getId());
        if (order.getAmount().compareTo(BigDecimal.ZERO) <= 0) {
            order.setStatus(Order.OrderStatus.ERROR);
        }
    }
});
// 4) IN_PROGRESS 상태일 때 실행하며, 상태를 PROCESSED로 변경한다.
OrderProcessStep processPaymentStep = new OrderProcessStep(order -> {
    if (order.getStatus() == Order.OrderStatus.IN_PROGRESS) {
        System.out.println("Processing payment of order " + order.getId());
        order.setStatus(Order.OrderStatus.PROCESSED);
    }
});
// 5) ERROR 상태일 때 실행하며, 에러 및 ID를 출력하도록 한다.
OrderProcessStep handleErrorStep = new OrderProcessStep(order -> {
    if (order.getStatus() == Order.OrderStatus.ERROR) {
        System.out.println("Sending out 'Failed to process order' alert for order " + order.getId());
    }
});
// 6) PROCESSED 상태일 때 실행하며, 정상적으로 종료 되었음을 알려준다.
OrderProcessStep completeProcessingOrderStep = new OrderProcessStep(order -> {
   if (order.getStatus() == Order.OrderStatus.PROCESSED) {
       System.out.println("Finished processing order " + order.getId());
   }
});

// work flow 생성
OrderProcessStep chainedOrderProcessSteps = initializeStep
                .setNext(setOrderAmountStep)
                .setNext(verifyOrderStep)
                .setNext(processPaymentStep)
                .setNext(handleErrorStep)
                .setNext(completeProcessingOrderStep);
// ---------------------------
```

6가지의 step을 생성하고, 이를 차례로 체이닝해주었다. 각자 processing 할 수 있는 것들만 process하기 때문에 에러 상태가 중간 step이 존재하더라도 다음 step에서 처리하지 않는다.

##### 정상적인 order

```java
Order order = new Order()
                .setId(1001L)
                .setStatus(Order.OrderStatus.CREATED)
                .setOrderLines(Arrays.asList(
                        new OrderLine().setAmount(BigDecimal.valueOf(1000)),
                        new OrderLine().setAmount(BigDecimal.valueOf(2000))
                ));

chainedOrderProcessSteps.process(order);
// => Start processing order 1001
//    Setting amount of order 1001
//    Verifying order 1001
//    Processing payment of order 1001
//    Finished processing order 1001
```

##### 잘못된 order (amount 값이 음수가 됨)

```java
Order failingOrder = new Order()
        .setId(1002L)
        .setStatus(Order.OrderStatus.CREATED)
        .setOrderLines(Arrays.asList(
                new OrderLine().setAmount(BigDecimal.valueOf(1000)),
                new OrderLine().setAmount(BigDecimal.valueOf(-2000))
        ));

chainedOrderProcessSteps.process(failingOrder);
// => Start processing order 1002
//    Setting amount of order 1002
//    Verifying order 1002
//    Sending out 'Failed to process order' alert for order 1002
```

