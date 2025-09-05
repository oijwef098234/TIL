> 해당 클래스는 예외처리를 하는 클래스이다.

### @Valid

> 해당 어노테이션은 dto의 필드에 유효성 검사를 적용하고 검사의 결과를 뽑아낸다.

코드를 보면서 더욱 쉽고 빠르게 이해해보자

```java
package com.example.boardcrud.Controller;

import com.example.boardcrud.Service.CreateService;
import com.example.boardcrud.Service.DeleteService;
import com.example.boardcrud.Service.ReadService;
import com.example.boardcrud.Service.UpdateService;
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

다음과 같이 @Valid라는 어노테이션을 붙이면 그 뒤에 나오는 dto가 유효성 검사가 적용된 상태로 나오게 된다.

---

그렇다면 우리는 이것을 왜 사용하는가?

## 왜 사용하는가?

> 사용자가 보낸 요청 dto가 정해진 형식에 맞는지 검사하기 위해서 사용한다.

---

## 그럼 뽑아낸 검사 결과는 어디에 저장하느냐?

> 바로 **BindingResult라는 객체에 저장한다.**

### **BindingResult란**

> 위에서 한 말 그대로 유효성 검사결과를 저장하고 있다.

그렇기 때문에

```java
package com.example.boardcrud.Controller;

import org.springframework.http.ResponseEntity;
import org.springframework.validation.FieldError;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;

import java.util.List;
import java.util.stream.Collectors;

@ControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<List<String>> handleMethodArgumentNotValidException(MethodArgumentNotValidException e) {
        List<String> errors = e.getBindingResult() // 이렇게 사용된다.
                .getFieldErrors()
                .stream()
                .map(FieldError::getDefaultMessage)
                .collect(Collectors.toList());
        return ResponseEntity.badRequest().body(errors);
    }
}
```

그럼 저기서 FieldErrors는 뭘까?

### FieldErrors란

> **BindingResult**는 실패하던 성공하던 모든 결과에 대한 정보를 담고 있지만 이것은 실패한 필드에 대한 정보만 가지고 있다.

그래서 `.getFieldErrors()` 이 부분을 보면 실패한 필드만 가져오게 하는 것을 알 수 있다.

그럼 여기서 또 의문이 들 수 있다.

`@ExceptionHandler(MethodArgumentNotValidException.class)` 은 뭘까?

### @ExceptionHandler(MethodArgumentNotValidException.class)란?

> 그냥 해당 클래스가 호출되면 이 클래스로 넘어도록 하는것이다.

### ResponseEntity<List<String>>

> 이거 처음보면 모양땜에 당황할 수도 있는데 이건 그냥 List<String>을 http응답으로 다시 한번 감싼거임 걍

그리고

```java
    public ResponseEntity<List<String>> handleMethodArgumentNotValidException(MethodArgumentNotValidException e) {
        List<String> errors = e.getBindingResult() // 이렇게 사용된다.
                .getFieldErrors()
                .stream()
                .map(FieldError::getDefaultMessage)
                .collect(Collectors.toList());
        return ResponseEntity.badRequest().body(errors);
    }
```

이 부분은 설명하기 귀찮으니까 대충 표로 정리함

|키워드|설명|
|---|---|
|`MethodArgumentNotValidException e`|유효성 검사 실패 시 전달되는 예외 객체|
|`e.getBindingResult()`|예외에서 유효성 검사 결과(BindingResult)를 가져옴|
|`.getFieldErrors()`|실패한 필드 목록 (`List<FieldError>`) 추출|
|`.stream()`|리스트를 스트림으로 변환 (Java Stream API)|
|`.map(FieldError::getDefaultMessage)`|각 필드 에러에서 메시지만 추출|
|`.collect(Collectors.toList())`|추출한 메시지를 다시 리스트로 묶음|
|`ResponseEntity.badRequest().body(errors)`|HTTP 상태코드 400과 함께 에러 메시지 리스트를 응답으로 전송|

---