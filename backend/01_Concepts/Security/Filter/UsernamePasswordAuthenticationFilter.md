## UsernamePasswordAuthenticationFilter란?

> 사용자가 인증 요청을 하면 사용자의 정보를 가로채서 인증 절차를 진행함

---

## 언제실행되냐?

> 사용자가 로그인 요청(`POST /login` 기본 경로)을 보낼 때 실행됨.

- 정확히는 **요청 URL + HTTP 메서드**가 맞을 때 작동함
    - URL 기본값: `/login`
    - HTTP 메서드: `POST`
- 이 조건을 만족하는 요청이면 이 필터가 **가장 먼저 동작함**

---

## 무슨 일을 하냐?

1. **요청 가로채기**
    - 클라이언트가 보낸 로그인 폼에서 파라미터 추출 (`username`, `password`).
2. **토큰 객체 생성**
    - `UsernamePasswordAuthenticationToken`을 만들어서 "이 사람이 로그인 시도했음"을 표시.
3. **인증 매니저 호출**
    - `AuthenticationManager`에게 이 토큰을 전달해서 검증을 맡김.
    - 실제 검증은 `AuthenticationProvider`(대표적으로 `DaoAuthenticationProvider`)가 담당.
4. **성공/실패 처리**
    - 성공 → `SecurityContext`에 인증된 사용자 저장, 이후 요청부터 인증된 상태 유지.
    - 실패 → `AuthenticationFailureHandler` 실행 (보통 로그인 실패 페이지나 401 응답).

---