## @ManyToOne

> 여러개의 자식이 하나의 부모에 속한다

→ 외래키는 수가 더 많은 자식쪽에 생성됨.

## @OneToMany

> 하나의 부모가 여러개의 자식을 가진다.

이제 사용하는 방법을 알아보자.

코드를 보면서 이해해보자, 상황은 게시글에서 댓글과 게시글의 관계이다.

### 자식 쪽 코드

```java
@Entity
public class Comment {

    @Id @GeneratedValue
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY, optional = false) // 외래키 가진 쪽 = 소유자
    @JoinColumn(name = "post_id", nullable = false)      // DB not null 제약
    private Post post;

    private String content;

    // 연관관계 편의 메서드 사용 시 setter는 package/private로 두기도 함
    public void setPost(Post post) { this.post = post; }
}
```

### 부모 쪽 코드

```java
@Entity
public class Post {

    @Id @GeneratedValue
    private Long id;

    private String title;

    @OneToMany(
        mappedBy = "post",                     // 연관관계 주인 아님(읽기 전용)
        cascade = CascadeType.ALL,             // Post 저장/삭제가 Comment에 전파
        orphanRemoval = true                   // 컬렉션에서 빠진 자식 자동 삭제
    )
    @OrderBy("id ASC")                         // 조회 시 정렬(선택)
    private List<Comment> comments = new ArrayList<>();

    /** 연관관계 편의 메서드(양쪽 동기화가 중요) */
    public void addComment(Comment c) {
        comments.add(c);
        c.setPost(this);
    }
    public void removeComment(Comment c) {
        comments.remove(c);
        c.setPost(null);
    }
}
```

---

## 옵션에 대해서 알아보자.

### fech

- @ManyToOne : LAZY
    - 이 옵션은 로딩이 필요 없는 항목을 제목만 읽는대도 본문까지 불러오는 불필요한 비용을 낭비하는것을 방지하기 위해 사용한다.
- @OneToMany : 기본 LAZY

---

- **orphanRemoval = true**: 부모 컬렉션에서 빠진 자식을 **자동 삭제**. (부모 변경 시 고아 생성 주의)