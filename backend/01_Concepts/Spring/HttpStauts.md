# HttpStatus이란?

> **스프링 프레임워크**가 제공하는 HTTP 상태코드 열거형(enum)

- 상태코드와 상태메세지를 타입으로 구분해 안전하게 사용이 가능하게 함

---

# 핵심 개념

- **역할**: 100~599까지의 표준 HTTP 상태를 상수로 제공
    
    예) `OK(200)`, `CREATED(201)`, `NO_CONTENT(204)`, `BAD_REQUEST(400)`, `UNAUTHORIZED(401)`, `FORBIDDEN(403)`, `NOT_FOUND(404)`, `CONFLICT(409)`, `INTERNAL_SERVER_ERROR(500)`, `SERVICE_UNAVAILABLE(503)` 등
    
- **시리즈(Series)**: 상태코드를 1xx~5xx 묶음으로 분류
    
    `INFORMATIONAL(1xx)`, `SUCCESSFUL(2xx)`, `REDIRECTION(3xx)`, `CLIENT_ERROR(4xx)`, `SERVER_ERROR(5xx)`
    

# 자주 쓰는 메서드

- `int value()` → 정수 코드 반환 (예: `HttpStatus.OK.value()` → 200)
- `String getReasonPhrase()` → “OK”, “Created” 같은 사유문구
- `Series series()` → 어떤 1xx~5xx 그룹인지
- 정수 → enum 변환
    - `HttpStatus.resolve(int)` → 없으면 `null` (안전)
    - `HttpStatus.valueOf(int)` → 없으면 예외 발생(주의)

```java
package com.example.springboot_practice.global.error;

import com.example.springboot_practice.global.error.exception.ClimException;
import com.example.springboot_practice.global.error.exception.ErrorCode;
import lombok.extern.slf4j.Slf4j;
import org.hibernate.exception.ConstraintViolationException;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@Slf4j
@RestControllerAdvice
public class GlobalExceptionHandler {

    //비즈니스 로직에서의 에러
    @ExceptionHandler(ClimException.class)
    public ResponseEntity<ErrorResponse> handClimException(ClimException e) {

        ErrorCode errorCode = e.getErrorCode();
        ErrorResponse response = ErrorResponse.of(errorCode, errorCode.getErrorMessage());
        e.printStackTrace();

        return new ResponseEntity<>(response, HttpStatus.valueOf(errorCode.getStatusCode())); // 여기서 httpStatus사용
    }

    // validation 에러
    @ExceptionHandler(ConstraintViolationException.class)
    public ResponseEntity<ErrorResponse> handleConstraintViolationException(ConstraintViolationException e) {
        ErrorCode errorCode = ErrorCode.BAD_REQUEST;
        ErrorResponse response = ErrorResponse.of(errorCode, errorCode.getErrorMessage());
        e.printStackTrace();

        return new ResponseEntity<>(response, HttpStatus.valueOf(errorCode.getStatusCode())); // 여기서 httpStatus사용
    }

    //그 외 에러들
    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handlerException(Exception e) {
        ErrorCode errorCode = ErrorCode.INTERNAL_SERVER_ERROR;
        ErrorResponse response = ErrorResponse.of(errorCode, e.getMessage());

        return new ResponseEntity<>(response, HttpStatus.INTERNAL_SERVER_ERROR); // 이렇게 아예 enum안에서 만들어져있는 에러코드 사용 가능
    }
}
```

---