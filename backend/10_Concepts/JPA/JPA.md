> JPA는 자바 객체를 데이터베이스에 쉽게 저장하고 불러올 수 있도록 도와주는 기능

예를 들어보자

예전에는 자바로 데이터베이스를 조작하려면

JDBC API이라는 방법으로 코딩하는것이다.

```java
Connection conn = ...;
PreparedStatement stmt = conn.prepareStatement("SELECT * FROM users WHERE id = ?");
stmt.setLong(1, id);
ResultSet rs = stmt.executeQuery();
```

이런 식으로 작성해야만 했다.

이 코드를 간략하게 설명하자면 db에서 아이디를 1씩 증가시키며 유저를 찾는 코드인데,

이렇게 작성하면

- 걍 너무 귀찮다
- 가독성이 너무 떨어진다.
- 실수가 잦아진다.

그래서 만들어진 방법이 JPA인 것이다.

jpa를 이용하여 코드를 작성하면

### User클래스

```java
@Entity
public class User {

    @Id
    @GeneratedValue
    private Long id;

    private String name;
    private String email;

    protected User() {}

}
```

### `UserRepository` 인터페이스

```java
public interface UserRepository extends JpaRepository<User, Long> {
    // 기본적인 CRUD는 JpaRepository가 다 제공!
}
```

### 실제 조회 코드 (JPA 스타일)

```java
User user = userRepository.findById(id)
    .orElseThrow(() -> new RuntimeException("유저를 찾을 수 없습니다"));
```

이런식으로 아주 좋은 가독성으로 체계적으로 코드를 작성할 수 있다.
