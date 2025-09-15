## @Slf4j란?

> 클래스에 붙이면 SLF4J 로거 필드를 자동으로 생성해주는 어노테이션

여기서 로거필드가 뭘까?

## 로거필드란?

> 프로그램이 언제, 어디서, 무슨 일이 있었는지 하나하나 다 적어두는 일지같은 느낌이다.

예시를 보자면

```java
import lombok.extern.slf4j.Slf4j;

@Slf4j
public class SignupService {
    public void signup(Long id) {
        log.info("회원가입 시작 id={}", id);   // INFO 로그
        try {
            // ... 회원 저장 로직
            log.debug("DB 저장 완료 id={}", id); // DEBUG 로그(개발 시 상세)
        } catch (Exception e) {
            log.error("회원가입 실패 id={}", id, e); // ERROR + 스택트레이스
        }
    }
}
```

이런 코드가 있다고 가정했을때

```java
2025-09-15 16:20:31 INFO  SignupService - 회원가입 시작 id=7
2025-09-15 16:20:31 ERROR SignupService - 회원가입 실패 id=7
java.lang.RuntimeException: ...
    at ...
```

위와 같은 로그가 나온다

---