## Lambda 형식이란?

> 간단한 함수를 간편하게 구현할 수 있는 문법의 종류

## 기본 문법

```java
// (파라미터) -> 표현식
x -> x * x

// (파라미터) -> { 문장들; return 값; }
(int a, int b) -> { int s = a + b; return s; }

// 파라미터 타입/괄호는 추론되면 생략 가능
a -> a * a
```

이런식으로 → 와 같은 기호를 사용하여 표현한다.

---

그럼 프로젝트에서 사용도니 람다 형식에 대해서 분석해보자.

```java
package com.example.post.domain.service;

import com.example.post.domain.dto.request.CommentRequest;
import com.example.post.domain.entity.CommentEntity;
import com.example.post.domain.entity.PostEntity;
import com.example.post.domain.repository.CommentRepository;
import com.example.post.domain.repository.PostRepository;
import jakarta.persistence.EntityNotFoundException;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;

@Service
@RequiredArgsConstructor
public class CreateComment {
    private final CommentRepository commentRepository;
    private final PostRepository postRepository;

    public void createComment(Long postId, CommentRequest comment) {
        PostEntity post = postRepository.findById(postId).orElseThrow(
                () -> new EntityNotFoundException("게시글이 존재하지 않습니다.")
        );

        CommentEntity commentEntity = new CommentEntity();

        commentEntity.setPostEntity(post);
        commentEntity.setContent(comment.getComment());

        commentRepository.save(commentEntity);
    }

}
```

이 코드에서 람다 형식이 쓰인 부분은 하나이다.

```java
public void createComment(Long postId, CommentRequest comment) {
    PostEntity post = postRepository.findById(postId).orElseThrow(
           () -> new EntityNotFoundException("게시글이 존재하지 않습니다.")
    );
```

## 이 형태의 람다식 해석

- 형태: `() -> new EntityNotFoundException("...")`
- 의미: **파라미터가 없는 함수**(0-인자)이고, 호출되면 `EntityNotFoundException` 객체를 하나 만들어서 “내준다”.

## 어떻게 동작하냐?

1. `postRepository.findById(postId)`가 `Optional<PostEntity>`를 반환
2. 값이 **있으면**: 그 값을 반환하고, **람다는 실행되지 않는다.**
3. 값이 **없으면**: `orElseThrow`가 **람다를 호출**해서 예외 인스턴스를 만들고 **던진다**.

---

## 그럼 람다 형식을 왜 사용하느냐?

- 클래스를 만들지 않고도 간결하게 표현할 수 있다.