## SecurityConfig란?

> 기본적인 Spring Security에 대한 모든 설정 파일을 말함

- 어떤 URL을 **인증 없이 접근 가능**하게 둘지 (`permitAll`)
- 어떤 URL은 **인증(로그인)** 또는 **인가(권한)**가 필요할지
- 세션 / 쿠키 / JWT 같은 **인증 방식** 결정

```java
@Configuration
@EnableWebSecurity
@RequiredArgsConstructor
public class SecurityConfig {

    private final JwtFilter jwtFilter;

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .csrf(csrf -> csrf.disable()) // CSRF 보호 비활성화(REST API에서 보통 끔)
            .sessionManagement(session -> session.sessionCreationPolicy(SessionCreationPolicy.STATELESS)) // 세션 사용 안 함 (JWT 방식)
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/auth/**").permitAll()  // 회원가입, 로그인은 누구나 접근 가능
                .anyRequest().authenticated()             // 나머지는 인증 필요
            )
            .addFilterBefore(jwtFilter, UsernamePasswordAuthenticationFilter.class); // JWT 검증 필터 추가

        return http.build();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder(); // 비밀번호 암호화
    }
}
```

- 누가, 어떤 요청을, 어떤 방식으로 인증/인가할지 정해줌

---

## FilterConfig란?

> SecurityConfig에 사용자의 정보를 판단하기 위해 그 정보를 controller로 부터 가져오는 역할을 함

보통 JWT 인증을 직접 구현할 때 만든다.

Spring Security는 **Filter 체인 구조**라서, 요청이 오면 여러 필터를 거치는데, 여기서 우리가 커스텀 필터를 끼워넣는 것.

```java
@Component
@RequiredArgsConstructor
public class JwtFilter extends OncePerRequestFilter {

    private final JwtProvider jwtProvider;

    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain filterChain) throws IOException, ServletException {

        // 1) 헤더에서 Authorization 꺼내기
        String token = jwtProvider.resolveToken(request);

        // 2) 토큰 검증
        if (token != null && jwtProvider.validateToken(token)) {
            Authentication auth = jwtProvider.getAuthentication(token); 
            // → UsernamePasswordAuthenticationToken 만들어줌

            // 3) SecurityContext에 인증 정보 저장
            SecurityContextHolder.getContext().setAuthentication(auth);
        }

        // 4) 다음 필터로 넘기기
        filterChain.doFilter(request, response);
    }
}
```