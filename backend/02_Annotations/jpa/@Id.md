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