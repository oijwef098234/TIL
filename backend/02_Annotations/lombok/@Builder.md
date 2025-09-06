### @Builder

> 값을 넣는 방식중 하나임 코드로 봐야지 한번에 이해가 가능함

```java
@Builder
public class Post {
    private String title;
    private String content;
    private String userName;
}
```

- 이런 코드에 @Builder가 붙어있다고 가정해보자 그럼

```java
Post post = Post.builder()
    .title("제목")
    .content("내용")
    .userName("홍길동")
    .build();
```

- 이런 방식으로 객체를 만들 수 있다.
- 하지만 그럼 Setter을 쓰면 되는거 아니야? 라고 생각할 수 있는데

문제점이 많이 생긴다…

### 1. 객체가 불안정 하다

이걸 처음 보면 뭔말인가 싶을수도 있는데 코드로 확인해보자

```java
Post post = new Post();  // 객체는 만들어졌지만...
post.setTitle("제목");
// content, userName은 아직 없음!
```

- 지금처럼 다른 값들은 저장을 안한상태로 db에 저장을 한다면 어떤 값이 들어갈지 모르기때문에 불안정하다는 것이다.

### 2. 객체가 변경 가능해서 오류 발생 가능함

이것도 그냥 코드를 보면서 이해하자

```java
Post post = new Post();
post.setTitle("처음 제목");

// 어떤 다른 코드에서 수정함
post.setTitle("나중에 누가 또 바꿈!");
```

이렇게 저장을 한다면 코드를 읽고 해석하는 입장이나 아니면 연관된 값들이 post에 들어가야만하는데 값이 하나만 바뀐다면 실행을 하는데 오류가 발생 할 수 있음