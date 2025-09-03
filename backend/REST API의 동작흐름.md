### REST API의 동작흐름

> [클라이언트] → [요청] → [서버 컨트롤러 → 서비스 → DB] → [응답] → [클라이언트]

### 요청 방식

1. 클라이언트가 URL형식으로 요청함

```json
GET /posts/1
```

1. controller가 요청을 받음

```java
@RestController
@RequiredArgsConstructor
@RequestMapping("/posts")
public class PostController {

    private final PostService postService;

    @GetMapping("/{id}")
    public PostResponse getPost(@PathVariable Long id) {
        return postService.getPostById(id);
    }
}
```

1. 서비스에서 요청을 처리함

```java
@Service
@RequiredArgsConstructor
public class PostService {

    private final PostRepository postRepository;

    public PostResponse getPostById(Long id) {
        Post post = postRepository.findById(id)
            .orElseThrow(() -> new RuntimeException("게시글이 없습니다"));
        return new PostResponse(post.getId(), post.getTitle(), post.getContent());
    }
}
```

1. Repository가 db에서 조회

```java
public interface PostRepository extends JpaRepository<Post, Long> {
    // JPA가 자동 구현
}
```

1. 클라이언트에세 JSON으로 답장

```json
{
  "id": 1,
  "title": "첫 번째 글",
  "content": "내용입니다"
}
```

---