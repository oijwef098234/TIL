> 이 어노테이션은 http url요청에서 파라미터를 메서드의 메게변수로 직접 매핑해주는 역할을 한다.

```java
GET /search?keyword=java&sort=recent
```

예를 들어 이런 url이 있다고 가정해보자

그럼 이걸 처리하는 컨트롤러는 다음과 같이 작성될 수 있다.

```java
@GetMapping("/search")
public String search(@RequestParam String keyword, @RequestParam String sort) {
    System.out.println("keyword: " + keyword);
    System.out.println("sort: " + sort);
    return "result";
}
```

이렇게 하면

- keyword = java
- srot = recent

가 들어간다.
