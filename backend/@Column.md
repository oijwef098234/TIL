> db에 들어가는 컬럼을 세부조정하는 어노테이션

세부조정이기 때문에 여러가지 옵션이 붙는다.

|옵션|목적 & 효과|예시 코드 → 생성 DDL|
|---|---|---|
|`name`|**DB 컬럼명** 직접 지정 (필드명과 다른 경우)|`@Column(name = "user_email")`→ `user_email varchar(255)`|
|`nullable`|`false` → **NOT NULL** 제약|`@Column(nullable = false)`→ `name varchar(30) not null`|
|`length`|문자열 **최대 길이** 지정|`@Column(length = 50)`→ `phone varchar(50)`|
|`unique`|**중복 금지**(UNIQUE)|`@Column(unique = true)`→ `alter table ... add constraint UK_email unique (email)`|
|`precision / scale`|**정수/소수 자리수**(DECIMAL)|`@Column(precision = 10, scale = 2)`→ `price decimal(10,2)`|
|`columnDefinition`|원시 SQL로 **타입·기본값·CHECK** 등 직접 기술|`@Column(columnDefinition = "char(1) default 'Y'")`→ `active_yn char(1) default 'Y'`|

그래서 코드로 보면 다음과 같다

```java
@Entity
@Table(name = "member",
       uniqueConstraints = @UniqueConstraint(name = "UK_member_email", columnNames = "email"))
public class Member {

    @Id @GeneratedValue
    private Long id;

    @Column(nullable = false, length = 30)          // NOT NULL, varchar(30)
    private String name;

    @Column(unique = true, length = 50)             // UNIQUE, varchar(50)
    private String email;

    @Column(precision = 8, scale = 2)               // decimal(8,2)
    private BigDecimal balance;

    @Column(columnDefinition = "char(1) default 'Y'") // 기본값 'Y'
    private String activeYn;
}
```