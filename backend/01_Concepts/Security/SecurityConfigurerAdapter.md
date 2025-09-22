## SecurityConfigurerAdapter란?

> HttpSecurity 같은 보안 빌더에다가 “네가 만든 보안 설정을 꽂을 수 있도록 확장 포인트”를 제공

- 스프링 시큐리티의 기본 보안 조립 라인에 **커스텀 규칙**을 끼워넣고 싶을 때 쓰는 어댑터

## 왜 쓰는가?

- 원래라면 `HttpSecurity` 안에서 직접 `addFilterBefore(...)`, `.csrf().disable()` 같은 걸 전부 작성해야 해.
- 그런데 공통되는 필터 등록 로직이나, 재사용 가능한 커스텀 보안 구성을 **한 클래스로 묶고 싶을 때** `SecurityConfigurerAdapter`를 상속해서 구현해.

## 내부 구조 (중요 메서드)

- `init(B builder)`
    
    → 빌더 초기화 단계에서 호출되는 훅 (선택적으로 오버라이드).
    
- `configure(B builder)`
    
    → **핵심!** 필터 추가, 인증/인가 규칙 등록 등을 여기서 구현.
    
- `SecurityConfigurerAdapter` 자체가 `SecurityConfigurer` 인터페이스를 기본 구현해둔 거라서,
    
    네가 필요한 메서드만 골라 재정의하면 됨.
    

---