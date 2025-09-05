# @JoinColumn
> 외래키에 대한 설정을 하는 컬럼

사용 예시를 보고 옵션에 대해서 알아보자.

```java
@Entity
@Table(name = "comment",
       indexes = @Index(name = "idx_comment_post_id", columnList = "post_id"))
public class Comment {

    @Id @GeneratedValue
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY, optional = false) // 관계 자체는 null 허용 안 함
    @JoinColumn(name = "post_id", nullable = false,      // DB 칼럼도 NOT NULL
                foreignKey = @ForeignKey(name = "fk_comment_post"))
    private Post post;

    private String content;
}
```

| 옵션                         | 의미 / 언제 쓰나                   | 비고                                                                                         |
| -------------------------- | ---------------------------- | ------------------------------------------------------------------------------------------ |
| `name`                     | FK 칼럼 이름                     | 기본값: `필드명_참조PK칼럼명` (예: `post_id`). 명시를 권장.                                                 |
| `referencedColumnName`     | **어느 칼럼을 참조할지** (대상 엔티티의 칼럼) | 기본은 대상 엔티티의 **PK 칼럼**. PK가 아닌 **유니크 칼럼**을 참조할 때만 지정.                                       |
| `nullable`                 | DB에서 **NOT NULL** 제약 설정      | `@ManyToOne(optional=false)`는 **엔티티 레벨** 제약, `nullable=false`는 **DB 레벨** 제약. 둘 다 맞추는 걸 권장. |
| `unique`                   | FK 칼럼에 **유니크 제약**            | 외래키로 **일대일(1:1)** 을 강제하고 싶을 때 사용. (N:1을 1:1처럼 제한)                                          |
| `insertable` / `updatable` | 칼럼에 대해 **쓰기 금지**             | 같은 칼럼을 **두 필드에 중복 매핑**할 때 한쪽을 읽기 전용으로 둠.                                                   |
| `columnDefinition`         | DDL 타입/제약 직접 지정              | 보통 자동 생성에 맡기고, 특별한 타입/기본값이 필요할 때만 사용.                                                      |
| `foreignKey`               | FK 제약의 **이름/모드** 지정          | `@ForeignKey(name="fk_xyz")`로 이름 고정.                                                       |
| `table`                    | FK 칼럼이 위치할 **테이블**           | 엔티티가 `@SecondaryTable`을 쓸 때 드물게 사용. 대부분 필요 없음.                                             |