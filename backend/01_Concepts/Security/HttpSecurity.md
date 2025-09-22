## HttpSecurity란?

> 다른 클래스에서 만들어진 어려가지 필터들을 체인 형태로 연결시켜줌

## 흐름을 더 풀어주면

1. **필터는 클래스 단위로 따로따로 존재**해.
    
    - `JwtTokenFilter` : 토큰 검증
    - `ExceptionFilter` : 에러 JSON 변환
    - `UsernamePasswordAuthenticationFilter` : 폼 로그인
    - `FilterSecurityInterceptor` : 최종 권한 체크
2. 그냥 놔두면 각자 흩어져 있는 필터들이 **아무 순서 없이 따로 놀게 돼**.
    
    → 누가 먼저 실행될지 알 수 없으면, 보안이 깨지거나 동작이 꼬임.
    
3. 그래서 **HttpSecurity가 빌더 역할**을 해서:
    
    - “이 필터는 저 필터 앞에”,
        
    - “이 URL은 누구만 접근 가능”,
        
    - “세션 쓸지 말지”…
        
        이런 걸 다 선언적으로 쌓아둔 뒤,
        
4. `http.build()`를 호출하면 **DefaultSecurityFilterChain**이라는 **완성된 필터 체인**을 만들어서 등록해.
    
    이제 모든 요청은 **그 체인에 들어있는 필터들을 순서대로 거쳐서** 컨트롤러에 도달해.
    

---