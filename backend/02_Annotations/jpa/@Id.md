## @id란?

> 테이블의 레코드를 고유하게 식별할 수 있는 필드에 붙이는 어노테이션

### 예시 코드

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(name = "username")
    private String username;
    
    // Getters and setters
}
```

- 이렇게 id와 같이 하나의 레코드를 고유하게 식별할 수 있는 값에 사용함

---

## 주의할점

- @Id어노테이션이 두개인데 이 어노테이션들의 차이를 알아보자

### 1) `jakarta.persistence.Id` (JPA/Hibernate용)

- **어디서 씀?**: RDBMS(MySQL, Postgres…) + JPA 엔티티
- **함께 쓰는 것들**: `@Entity`, `@Table`, `@GeneratedValue(IDENTITY/SEQUENCE/…)`
- **동작**: 영속성 컨텍스트, 지연로딩, 연관관계 매핑, JPQL 등 JPA 규칙을 따른다.
- **리포지토리**: `JpaRepository<T, ID>`

```java
@Entity
public class Member {
  @jakarta.persistence.Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  private Long id;
}
```

### 2) `org.springframework.data.annotation.Id` (Spring Data 공용: Redis/Mongo/Cassandra 등)

- **어디서 씀?**: NoSQL 계열 + Spring Data 저장소
- **함께 쓰는 것들**: `@RedisHash`, `@Document`(Mongo) 등
- **동작**: JPA 기능(연관관계, JPQL 등) 없음. 각 스토리지 모듈 규칙대로 key로만 식별.
- **리포지토리**: `CrudRepository<T, ID>` / `ReactiveCrudRepository<T, ID>`

```java
@RedisHash("refresh_tokens")
public class RefreshToken {
  @org.springframework.data.annotation.Id
  private String id; // Redis key
}
```