**DI (Dependency Injection)** 은 말 그대로

> 필요한 객체(의존성)를 외부에서 "주입"해주는 것을 말함

코드를 통해서 이해해보자

```java
@RequiredArgsConstructor
public class PostService {
    private final PostRepository postRepository;

    // 생성자 자동 생성됨:
    // public PostService(PostRepository postRepository) {
    //     this.postRepository = postRepository;
    // }
}
```

이런 느낌으로 생성이 되는것이다.

---

그럼 도대체 di를 왜 사용하는걸까?

### 1. 코드가 유연해진다.

- 여기서 코드가 유연해진다라는 말이 조금 이해가 안될수 잇다.
- 근데 유연하다는게 약간 조금 자유자재로 변환이 편하게 가능하다는거임

```java
public class Car {
    private final Engine engine = new GasEngine(); // 특정 구현체에 고정
}
```

- 이 코드를 보면 다른 엔진으로 바꿀때 아예 코드를 바꿔야하는 반면

```java
public class Car {
    private final Engine engine;

    public Car(Engine engine) {
        this.engine = engine;
    }
}

// 다양한 엔진
class GasEngine implements Engine { }
class ElectricEngine implements Engine { }

// 상황에 따라 선택
Car gasCar = new Car(new GasEngine());
Car electricCar = new Car(new ElectricEngine());
```

- 다음과 같이 외부에서 따로 불러오는 형식으로 코드를 작성하면 바꾸기가 휠씬 유연하다

### 2. 코드 재사용성이 증가하고, 유지보수가 쉽다.

```java
public class FeedService {
    private final Rice rice = new Rice(); // 직접 '밥' 정해버림

    public void feed() {
        rice.give();
    }
}

class Rice {
    public void give() {
        System.out.println("밥을 줬어요!");
    }
}
```

- 이렇게 적어버리면 로봇은 밥 밖에 못줌

```java
public interface Food {
    void give(); // 어떤 음식을 주든 이 메서드는 있어야 함
}
```

```java
public class Rice implements Food {
    public void give() {
        System.out.println("밥을 줬어요!");
    }
}

public class Meat implements Food {
    public void give() {
        System.out.println("고기를 줬어요!");
    }
}
```

- 이렇게 여러가지의 밥의 종류를 만들어주면

```java
Food rice = new Rice();
FeedService robot1 = new FeedService(rice);
robot1.feed();  // 👉 밥을 줬어요!

Food meat = new Meat();
FeedService robot2 = new FeedService(meat);
robot2.feed();  // 👉 고기를 줬어요!
```

- 이렇게 골라서 사용할 수 있다.
