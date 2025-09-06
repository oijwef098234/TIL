> 관심사 별로 여래개의 계층을 분리하여 만들어낸 아키텍쳐를 의미한다.

여기서 관심사가 무엇이냐면 각 계층마다 맡는 역할을 의미한다고 보면 된다.

우선 레이어드 아키텍쳐의 계층화로 표현된 그림을 보자

![image](../img/image.png)

크게 4가지의 계층으로 구분이 된다.

각 계층별로 소개하자면

### Presentation Layer

> 클라의 요청, 응답을 처리하는 책임을 지닌다.

**주 역햘**

- 클라이언트의 요청을 반환
- 기본적인 내용을 검증
- 수행 결과를 클라이언트에 반환

보통 spring에서 따지자면 controller가 한다.

```java
package com.example.boardcrud.Controller;

import com.example.boardcrud.Service.*;
import com.example.boardcrud.dto.Request.PostRequest;
import com.example.boardcrud.dto.Responsse.PostResponse;
import jakarta.validation.Valid;
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
    public void createPost(@Valid @RequestBody PostRequest postRequest) {
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
    public void updatePost(@Valid @RequestBody PostRequest postRequest, @PathVariable Long id) {
        updateService.updatePost(postRequest, id);
    }
}

```

그래서 @Valid와 같이 유효성 검사를 하는 역할또한 같이 한다.

### Business Layer

> 비지니스 로직을 수행하는 계층, 핵심적인 기능을 구현하고 수행하는 책임을 지님

```java
package com.example.boardcrud.Service;

import com.example.boardcrud.Entity.Post;
import com.example.boardcrud.Repository.PostRepository;
import com.example.boardcrud.dto.Request.PostRequest;
import com.example.boardcrud.exception.NoWordException;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
@RequiredArgsConstructor
public class CreateService {
    private final PostRepository postRepository;

    public void createPost(PostRequest postRequest) {
        String title = postRequest.getTitle();
        String content = postRequest.getContent();

        List<String> noWord = List.of("개병신", "씨발", "장애인", "엄마", "아빠");

        for(String word : noWord) {
            if(title.contains(word)) {
                throw new NoWordException("제목", word);
            }
        }

        for(String word : noWord) {
            if(content.contains(word)) {
                throw new NoWordException("본문", word);
            }
        }

        Post post = new Post(
                postRequest.getTitle(),
                postRequest.getContent(),
                postRequest.getUserName()
        );

        postRepository.save(post);
    }

}
```

- 커스텀 유효성 검사를 넣을 수 있고
- 기본적인 기능들을 구현하는 계층이다.
- _**Presentation Layer에 전달할 정보를 생성할 뿐 db에 관련된 로직은 알지 못한다.**_

### **Persistence Layer**

> db에 접근하여 db를 관리한다.

```java
package com.example.boardcrud.Service;

import com.example.boardcrud.Entity.Post;
import com.example.boardcrud.Repository.PostRepository;
import com.example.boardcrud.dto.Responsse.PostResponse;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.Optional;
import java.util.stream.Collectors;

@Service
@RequiredArgsConstructor
public class ReadService {
    private final PostRepository postRepository;

    public List<PostResponse> getPostRepository() {
        return postRepository.findAll()
                .stream()
                .map(PostResponse::new)
                .collect(Collectors.toList());
    }

    public PostResponse findById(Long id) {
        Post post = postRepository.findById(id) // 여기서 객체를 선언하고 dto를 이용하여 PostResponse형을 맞춰는거임
                .orElseThrow(() -> new RuntimeException("해당 게시글 없음")); // 그리고 orElseThrow는 예외 처리를 하는거임
        // 이거 예외처리 그냥 안하고 넘기면 안되냐 할 수도 있는데 찾아보니까 옵션값은 있을수도 있고 없을수도 있어서 꼭 예외처리를 해야한다고 함

        return new PostResponse(post); // 그리고 여기서 @Getter를 이용하여 PostResponse에서 자동으로 지원하는 getId를 이용하여 id를 생성자를 통해 가져옴
    }
}
```

이 코드에서도 보면 어디에 db관련 로직이 있는지 잘 모를거다.

하지만 findAll이나 findById같은 경우 다 repository에서 제공을 하는것임

그래서 **Persistence Layer**는 Repository와 매핑된다.

기본적인 역할은

- db에 접근하여 데이터들을 관리함
- 실제 SQL을 수행하여 데이터베이스 관리

### Database Layer

> 데이터들이 저장되는 실제 DB

난 이 레이어를 공부하며 다른 계층들과는 달라서 특별하다고 느꼈다.

다른 게층들은 코드 자체로 관심사를 분리하는데 이 계층은 진짜 db가 하나의 계층이 된다.

그래서 H2, MySQL같은 것들이 Database Layer에 속한다.

기본적인 역할은

- 데이터들을 저장함.

---
