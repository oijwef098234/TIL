## ObjectMapper란?

> **자바 객체를 JSON 문자열로 바꾸거나(JSON 직렬화)**, **JSON을 자바 객체로 바꾸는(역직렬화)** 도구

## 어디에 쓰나? (Spring 기준)

- 컨트롤러에서 `@RequestBody`/`@ResponseBody`로 JSON을 주고받을 때 **스프링이 내부에서 자동으로 쓰는 도구**가 바로 `ObjectMapper` 임

```java
@RestController
@RequiredArgsConstructor
public class UserController {
  private final ObjectMapper objectMapper; // 스프링이 주입

  @PostMapping("/echo")
  public String echo(@RequestBody UserDto dto) throws JsonProcessingException {
    // DTO → JSON 문자열
    return objectMapper.writeValueAsString(dto);
  }
}
```

---

## 게시판 CRUD에서 사용된 예시

```java
package com.example.post.global.error;

import com.example.post.global.error.exception.ErrorCode;
import com.example.post.global.error.exception.PostException;
import com.fasterxml.jackson.databind.ObjectMapper;
import jakarta.servlet.FilterChain;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import lombok.RequiredArgsConstructor;
import org.springframework.web.filter.OncePerRequestFilter;

import java.io.IOException;

@RequiredArgsConstructor
public class GlobalExceptionFilter extends OncePerRequestFilter {
    private final ObjectMapper objectMapper;

    @Override
    protected void doFilterInternal(
            HttpServletRequest request,
            HttpServletResponse response,
            FilterChain filterChain
    )throws IOException {
        try {
            filterChain.doFilter(request,response);
        } catch (PostException e){
            ErrorCode errorCode = e.getErrorCode();
            writerErrorResponse(response, errorCode.getStatus(), ErrorResponse.of(errorCode, errorCode.getMessage()));
        } catch (Exception e){
            e.printStackTrace();
            writerErrorResponse(response, response.getStatus(), ErrorResponse.of(response.getStatus(),e.getMessage()));
        }
    }

    private void writerErrorResponse(HttpServletResponse response, int statusCode, ErrorResponse errorResponse) throws IOException{
        response.setStatus(statusCode);
        response.setContentType("application/json");
        response.setCharacterEncoding("UTF-8");
        objectMapper.writeValue(response.getWriter(), errorResponse);
    }
}
```

- 여기서 writeValue는 객체 → JSON으로 바꿔주는 메서드이다.

---