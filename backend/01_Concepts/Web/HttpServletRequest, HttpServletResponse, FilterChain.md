# 1) HttpServletRequest — “들어온 요청 봉투”

클라이언트가 보낸 **HTTP 요청의 모든 정보**가 들어있는 객체.

- 예: 메서드/URI/헤더/쿼리파라미터/바디/쿠키/세션

# 2) HttpServletResponse — “보낼 응답 봉투”

서버가 **돌려줄 내용**을 채우는 객체.

- 예: 상태코드/헤더/바디(텍스트·바이너리)/쿠키

# 3) FilterChain — “다음 단계로 넘기는 컨베이어”

필터들 사이를 잇는 **체인 제어자**.

`doFilter(request, response)`를 호출해야 **다음 필터 → DispatcherServlet → 컨트롤러**로 진행됨. 호출 전/후에 코드를 넣어 **전/후 처리**가 가능.

- 안 부르면 **여기서 응답을 끝내는(차단하는)** 것.

---

## 예시코드

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
    protected void doFilterInternal( // 여기서 인자로 받아 로직을 통해 처리한다.
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