> 객체와 db의 컬럼의 구조가 다르기 때문에 변환을 해야하는데 그 중간에서 변환해주는 친구가 바로 ORM이다.

어떻게 다르냐면

|객체지향 (Java 등)|관계형 데이터베이스 (MySQL 등)|
|---|---|
|클래스 / 객체|테이블 / 레코드|
|필드 (속성)|컬럼 (열)|
|메서드|SQL 쿼리|

이런 식으로 되어있으므로 한번에 매핑이 되지 않는다.

예시를 보여주면

```java
// Java 클래스 (Entity)
public class User {
    private Long id;
    private String name;
    private String email;
}
```

이런 코드가 있다고 가정하자.

```sql
-- DB 테이블
CREATE TABLE user (
    id BIGINT PRIMARY KEY,
    name VARCHAR(255),
    email VARCHAR(255)
);
```

그럼 sql로 이렇게 변환이 되어 db에 저장이 된다.

따라서 ORM은 JPA의 핵심이라고 할 수 있다.

---