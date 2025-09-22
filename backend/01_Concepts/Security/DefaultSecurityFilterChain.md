## DefaultSecurityFilterChain란?

> Spring Security에서 **최종적으로 완성된 보안 필터 라인(체인)** 객체

## 뭐가 있냐

- **어떤 요청에 적용할지 조건**과
- **그 요청이 거쳐야 할 필터들의 순서**가 들어 있어.

## 만들어지는 과정

- 네가 `HttpSecurity`로 설정을 쌓은 뒤 `http.build()`를 호출하면 → `DefaultSecurityFilterChain` 객체가 생성됨