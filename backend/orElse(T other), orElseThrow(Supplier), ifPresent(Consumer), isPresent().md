### orElse(t other)

> 안에 값이 있으면 그 값을 반환하고 그게 아니라면 T other에 있는 값을 반환한다.

```java
import java.util.Optional;

public class Main {
    public static void main(String[] args) {
        Optional<String> optionalWithValue = Optional.of("Hello"); // 이렇게 사용함
        Optional<String> optionalEmpty = Optional.empty(); // 이렇게 사용한다.

        String value1 = optionalWithValue.orElse("Default");
        String value2 = optionalEmpty.orElse("Default");

        System.out.println(value1); // 출력: Hello
        System.out.println(value2); // 출력: Default
    }
}
```

### orElseThrow(Supplier)

> 만약 안에 값이 null이라면 따로 발생시키도록 만든 에러를 출력함

```java
import java.util.Optional;

public class Main {
    public static void main(String[] args) {
        Optional<String> optional = Optional.empty();

        String result = optional.orElseThrow(() -> new IllegalArgumentException("값이 없어요!"));
        // 실행 시: IllegalArgumentException: 값이 없어요!
    }
}
```

여기서 `new IllegaAraumentException("에러의 내용");` 이 부분이 따로 발생시키도록 만든 에러임

그래서 다음과 같이 쓰이기도 함

```java
public void setAge(int age) {
    if (age < 0) {
        throw new IllegalArgumentException("나이는 음수가 될 수 없어요!");
    }
    this.age = age;
}
```

여기선 “throw로 에러를 던진다”라는 표현을 사용함

### ifPresent(lvalue → {})

> 값이 있을 경우에만 코드가 동작함

그래서 값이 없는 경우엔 아무런 이벤트도 발생하지 않음

```java
import java.util.Optional;

public class Main {
    public static void main(String[] args) {
        Optional<String> optional = Optional.of("Spring");

        optional.ifPresent(value -> {
            System.out.println("값이 있어요: " + value);
        });

        // 출력: 값이 있어요: Spring
    }
}
```

코드를 보면 value값은 갑자기 어디서 나오는거지? 싶을 수 있다.

`value → {...}` 이런 형식을 람다식이라고 한다.

그래서 optional에 있는 값을 자동으로 value에 저장한다는 의미를 내포하고 있다.

### isPresent()

> isPresent()에서 괄호 안에 아무것도 안들어가기 때문에 여기선 true or false로 판단한다.

```java
import java.util.Optional;

public class Main {
    public static void main(String[] args) {
        Optional<String> optional = Optional.of("Java");

        if (optional.isPresent()) { // 여기서 값이 있으면 true
            System.out.println("값이 있습니다: " + optional.get());
        } else { // false일 때의 처리
            System.out.println("값이 없습니다.");
        }

        // 출력: 값이 있습니다: Java
    }
}
```

---