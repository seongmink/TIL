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

