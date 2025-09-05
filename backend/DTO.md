### DTO란?

> 데이터를 전달하기 위한 객체

그럼 엔티티를 쓰면 되는거 아니냐 라고 생각 할 수도 있는데 엔티티를 바로 쓰지 않는 이유는

entity가 직접적으로 사용되면 보안의 위험성도 커지고 또한 db과 관련된 중요한 값이기 때문에 함부로 바꾸면 에러가 발생할수도 잇다.

그래서 dto라는 상자를 따로 만들어서 요청을 보내고 받고 하는것이다.

예를들어서 이런 entity가 있다고 가정하자

```java
package com.example.boardcrud.Entity;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import lombok.*;

@Entity
@Getter
@Setter
@Builder
@NoArgsConstructor
@AllArgsConstructor

public class Post {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;
    private String content;
    private String userName;

    public Post(String title, String content, String userName) {
        this.title = title;
        this.content = content;
        this.userName = userName;
    }

    public void updatePost(String title, String content, String userName) {
        this.title = title;
        this.content = content;
        this.userName = userName;
    }
}
```

그럼 여기서 이런 파일 트리를 만든 다음에

![dto](../img/dto.png)

```java
package com.example.boardcrud.dto.Request;

import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
public class PostRequest {
    private String title;
    private String content;
    private String userName;
}
```

```java
package com.example.boardcrud.dto.Responsse;

import com.example.boardcrud.Entity.Post;
import lombok.*;

@Getter
@Builder
@NoArgsConstructor
@AllArgsConstructor

public class PostResponse {
    private Long id;
    private String title;
    private String content;
    private String userName;

    public PostResponse(Post post) { // dto 생성하는거임
        this.id = post.getId();
        this.title = post.getTitle();
        this.content = post.getContent();
        this.userName = post.getUserName();
    }
}
```

그 안에 이런 dto를 만들 수 있는데

여기서 의문이 들 수 있다.

dto는 entity값을 사용해야하는데 요청을 받는 클래스를 보면 따로 entity를 불러오지 않는다.

그 이유는

- 아까 말했던것처럼 받을때의 형식을 상자처럼 지정해서 필요한 값만 사용하기 위함

그리고 그래서 응답하는 클래스를 보면 따로 entity를 불러와줘서 dto를 사용한다.
