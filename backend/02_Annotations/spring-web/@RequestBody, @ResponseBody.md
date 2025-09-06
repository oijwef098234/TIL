### @RequestBody

> 클라이언트측에서 `JSON` 으로 요청을 보낼때 그 요청을 `spring`가 자동으로 `java`가 이해하기 쉽도록 변환해줌

- 다음과 같은 사용자의 요청이 있다고 가정해보자.

```json
POST /users
Content-Type: application/json

{
  "username": "chulsu",
  "email": "chulsu@email.com"
}
```

- 그러면 일단 user로 들어온 요청이니까

```java
@PostMapping("/users") // 이렇게 user에서 처리해준다.
public String createUser(@RequestBody UserRequest request) { // 여기서 요청을 해석하여 필요한 값을 매핑시킨다.
    return request.getUsername() + "님, 환영합니다!";
}
```

- 또한 UserRequest에 대한 코드를 보면

```java
public class UserRequest {
    private String username;
    private String email;

    public UserRequest() {}

    public String getUsername() { return username; }
    public void setUsername(String username) { this.username = username; }

    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
}
```

- JSON → UserRequesr에 있는 객체로 매핑해줌
- 따라서 클라이언트에서 들어온 요청을 잘 해석하여 코드에 맞게 필요한 값을 매핑시켜주는 역할을 하는 spring어노테이션이다.

---

### @ResponseBody

> 자바 메소드에서의 리턴값을 HTML뷰로 처리하지 않고 JSON응답으로 처리하는 역할을 해주는 Spring어노테이션이다.

여기서 HTML뷰가 무슨말이냐면

```java
@Controller
public class HelloController {

    @GetMapping("/hello")
    public String hello() {
        return "greeting";  // ← 이건 리턴값이 "greeting"이다.
    }
}
```

- 이런 코드가 있다고 가정하면 리턴이 되었을때 json으로 리턴이 되는것이 아닌
- greeting.html이라는 뷰로처리해버린다.

따라서 다시 원점으로 돌아가면

```java
public class UserResponse {
    private Long id;
    private String username;
    private String email;

    public UserResponse(Long id, String username, String email) {
        this.id = id;
        this.username = username;
        this.email = email;
    }

    public Long getId() { return id; }
    public String getUsername() { return username; }
    public String getEmail() { return email; }
}
```

```java
@GetMapping("/users/{id}")
@ResponseBody
public UserResponse getUser(@PathVariable Long id) {
    return new UserResponse(id, "chulsu", "chulsu@email.com");
}
```

- 이런 코드에서 `id, "chulsu", "chulsu@email.com"` 이런 값들을 반환하고 그 값들을 HTML뷰로 처리 하지 않기 위해서 `@ResponseBody` 가 꼭 필요한것이다.