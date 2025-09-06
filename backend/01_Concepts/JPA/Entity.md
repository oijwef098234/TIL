원래는 entity를 service, controller 등등과 같이 적으려고 했는데 얘만 따로 뭔가가 되게 많아서 이렇게 따로 적는거임

### Entity

> 데이터베이스와 1 : 1로 매핑되는 클래스이다.

그래서 데이터베이스에서 한(row)가 entity에선 하나의 객체로 표현된다.

entity예시 코드를 보면서 이해해보면

```java
package com.example.boardcrud.Entity;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import lombok.*;

@Entity // 이 클래스가 entity라는것을 명시해주는 어노테이션
@Getter
@Setter
@NoArgsConstructor // 이거 이따 설명해줌
@AllArgsConstructor // 이거 이따 설명해줌

public class Post {
    @Id // 이거 이따 설명해줌
    @GeneratedValue(strategy = GenerationType.IDENTITY) // 이거 이따 설명해줌
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

여기서

```java
private Long id;
private String title;
private String content;
private String userName;
```

- 이 부분은 내가 아까 db와 연결된다고 했으니까 이해가 갈것이다. 근데

```java
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
```

이 메서드들은 왜 왜있는지 어디에 사용하는지 이해가 가지 않을수도 있다.

우선 updatePost에 대해서 설명해자면 이 메소드를 자세히 살펴보면 title, content, userName값을 입력받고 새롭게 수정하는 메소드인것을 알 수 있다.

여기서 db에 메핑되는 클래스인데 왜 메소드가 있냐고 생각할수도 있다.

- 메소드의 역할을 보면 값을 수정하는거기 때문에 db에 값이 변동이 된다. 따라서 저런 기능을 하는 메소드는 있어도 된다.

---

그리고 이제 @Id와 @GeneratedValue(strategy = GenerationType.IDENTITY)에 대해서 알아보자

### @Id

> 해당 어노테이션이 붙어있는 필드값이 기본키라는것을 명시하는 어노테이션이다.

- 여기서 기본키란 중복된 값을 가질 수 없는 키를 말한다.

### @GeneratedValue(strategy = GenerationType.IDENTITY)

일단 이 코드를 끊어서 해석해봐야한다.

### @GeneratedValue

> 따로 값을 넣지 않아도 자동으로 값을 넣어준다는 어노테이션이다.

그리고 여기서

### (strategy = …)

> 어떤 방식으로 값을 채울지를 정하는 것이다.

### GenerationType.IDENTITY

> 값을 1, 2, 3의 방식으로 채우겠다는것임