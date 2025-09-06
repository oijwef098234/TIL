### @PathVariable

> 클라이언트가 보낸 URL에 있는 변수값을 메서드의 파라미터로 가져오는 역할을 한다.

- 만약에 이런 코드가 있다고 가정해보자

```java
@GetMapping("/users/{id}") // 만약에 /user/숫자 라는 요청이 들어오면 이 메서드로 처리해라
public String getUserById(@PathVariable Long id) {
    return "요청한 유저의 id는: " + id;
}
```

- 또한 클라이언트가 보낸 요청은 이런 형식이다.

```json
GET /users/10
```

- 그럼 여기서 10이라는 값을 `@PathVariable` 이 찾아서 연결해준다.

---

## 여러가지 사용방법들

### 여러개 받기

```java
@GetMapping("/users/{userId}/posts/{postId}")
public String getPost(
    @PathVariable Long userId, // 이런식으로 각 필드마다 붙여준다.
    @PathVariable Long postId
) {
    return "userId: " + userId + ", postId: " + postId;
}
```

- 만약에 이렇게 한다면 요청이 `/users/7/posts/3` 라는 식으로 들어왔을때
- `userId: 7, postId: 3` 이런 출력형식으로 나온다.

### 이름이 다를 때

```java
@GetMapping("/items/{itemCode}")
public String getItem(@PathVariable("itemCode") String code) {
    return "Item code is: " + code;
}
```

- 다음과 같이 이름이 다르다면 따로 지정을 하여 맞춰줄 수 있다.
- 만약에 이름이 같다면 다음과 같이 쓴다.

```java
@GetMapping("/items/{itemCode}")
public String getItem(@PathVariable String itemCode) {
    return itemCode;
}
```