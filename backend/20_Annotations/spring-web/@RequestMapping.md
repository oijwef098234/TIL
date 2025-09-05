### @RequestMapping

> 해당 어노테이션은 클라이언트의 URL요청을 그 역할에 맞는 코드와 연결해주는 역할을 하는 spring의 어노테이션이다.

- 따라서 다음과 같이 사용된다.
- 그래서 클라이언트한테 들어오는 요청중 `/hello` 로 들어오는 요청을 해당 메서드와 매핑하라는 의미를 가지고 있다.

```java
@RequestMapping("/hello")
public String sayHello() {
    return "Hello!";
}
```

- 또한 클래스 전체로 매핑할 수도 있다.

```java
package com.example.boardcrud.Controller;

import com.example.boardcrud.Service.CreateService;
import com.example.boardcrud.Service.DeleteService;
import com.example.boardcrud.Service.ReadService;
import com.example.boardcrud.Service.UpdateService;
import com.example.boardcrud.dto.Request.PostRequest;
import com.example.boardcrud.dto.Responsse.PostResponse;
import lombok.RequiredArgsConstructor;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequiredArgsConstructor
@RequestMapping("/boardcrud")
public class PostController {
    private final CreateService createService;
    private final ReadService readService;
    private final DeleteService deleteService;
    private final UpdateService updateService;

    @PostMapping("/create") // 생성
    public void createPost(@RequestBody PostRequest postRequest) {
        createService.createPost(postRequest);
    }

    @GetMapping("/all") // 전체 조회
    public List<PostResponse> getPostRepository() {
        return readService.getPostRepository();
    }

    @GetMapping("/{id}") //. 아이디조회
    public PostResponse findById(@PathVariable Long id) {
        return readService.findById(id);
    }

    @DeleteMapping("/{id}") // 삭제
    public void deleteById(@PathVariable Long id) {
        deleteService.deletePost(id);
    }

    @PatchMapping("/{id}")
    public void updatePost(@RequestBody PostRequest postRequest, @PathVariable Long id) {
        updateService.updatePost(postRequest, id);
    }
}
```

- 위 코드는 CRUD를 구현할 때 Controller에 있는 코드인데 `@RequestMapping("/boardcrud")` 이 줄은
- “앞으로 `/boardcrud` 로 들어오는 요청은 여기있는 모든 클래스와 매핑해주세요” 라는 의미를 내포한다.