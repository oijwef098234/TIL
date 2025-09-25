## SecurityConfig란?

> Spring Security에 대한 전체적인 설정을 다루는 곳

```java
package com.example.studyspringboot.global.security;

import com.example.studyspringboot.global.security.jwt.JwtTokenProvider;
import com.fasterxml.jackson.databind.ObjectMapper;
import lombok.RequiredArgsConstructor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.HttpMethod;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.web.SecurityFilterChain;

@RequiredArgsConstructor
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    private final JwtTokenProvider jwtTokenProvider;
    private final ObjectMapper objectMapper;

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        return http
                .csrf().disable()
                .formLogin().disable()
                .cors()

                .and()
                .sessionManagement()
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS)

                .and()
                .authorizeRequests()

                .antMatchers("/swagger-ui/**").permitAll()
                .antMatchers("/v3/api-docs/**").permitAll()

                // user
                .antMatchers(HttpMethod.POST, "/api/user/signup").permitAll()

                // auth
                .antMatchers("/auth/token").permitAll()

                // feed
                .antMatchers(HttpMethod.GET, "/feed/list").permitAll()
                .antMatchers(HttpMethod.GET, "/feed/{feed-id}").permitAll()
                .antMatchers(HttpMethod.GET, "/feed/search/{title}").permitAll()

                .anyRequest().authenticated()

                .and()
                .apply(new FilterConfig(jwtTokenProvider, objectMapper))

                .and().build();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

}
```

일단 이 코드를 보면 모르는게 많다.

차근차근 알아보아요 ㅎㅎ

---

## .disable()

> 앞에 나오는 특성을 비활성화, 끄기의 기능을 수행한다.

### 예시

```java
.csrf().disable() // csrf 보호기능 끄기
.formLogin().disable() // formLogin 끄기
```

## .csrf()

> 브라우저의 **쿠키 자동전송**을 악용해 사용자를 속여 요청을 보내게 하는 공격.

- **세션+쿠키** 기반의 웹앱(폼 로그인, 세션 유지)에서는 **켜두는 게 기본**.
- **JWT/토큰 기반 API**에서는?
    - 서버가 세션/쿠키에 의존하지 않고, 매 요청 **Authorization 헤더**에 토큰을 보냄.
    - 브라우저가 자동으로 헤더를 붙이지 않으니 **CSRF 표면이 매우 낮음**.
    - 그래서 보통 `csrf().disable()`로 꺼서 불필요한 403을 방지.

## .formLogin()

> 시큐리티가 기본적으로 제공하는 로그인 폼을 의미함

- 우리는 **REST API + JWT** 사용 → **화면**이 필요 없음.
- 기본 로그인 페이지가 노출되면 혼란만 줌. (API 서버는 JSON만 주고받게끔)

## .and()

> **체이닝 문법**에서 **하위 설정 블록을 끝내고 상위(HttpSecurity)로 돌아갈 때** 쓰는 연결자.

```java
http
  .csrf().disable()      // csrf() 블록 끝나고
  .formLogin().disable() // formLogin() 블록 끝나고
  .cors()                // cors() 블록 끝
  .and()                 // 상위로 복귀
  .sessionManagement()...
```

## .anyRequest()

> .requestMatchers()로 설정하고 남아있는 모든 요청에 대한 설정

- 마지막 **기본 규칙(Default rule)** 로 씀.

```java
http.authorizeHttpRequests(auth -> auth
    .requestMatchers("/public/**").permitAll()   // 공개
    .requestMatchers("/admin/**").hasRole("ADMIN") // 관리자만
    .anyRequest()                                // 그 외 전부
    .authenticated()                             // → 인증 필요
);
```

## .antMatchers()

> 특정 경로에 대한 인가 규칙을 선언하는 데 사

## .permitAll()

> **그 경로는 인증 없이 누구나 접근 허용**.

- `.antMatchers("/auth/token").permitAll()`

## .authenticated()

> 인증된 사용자만 허용

### 예시

```java
http.authorizeHttpRequests(auth -> auth
    .requestMatchers("/auth/login", "/docs/**").permitAll() // 로그인, 문서 공개
    .requestMatchers("/profile/**").authenticated()         // 로그인만 되어 있으면 OK
    .requestMatchers("/admin/**").hasRole("ADMIN")          // 관리자만
    .anyRequest().denyAll()                                 // 나머지는 전부 거부
);
```

## .sessionManagement()

> `HttpSecurity`의 **세션(HTTP 세션) 사용 방식**을 설정하는 블록을 여는 메서드

```java
.sessionManagement()
.sessionCreationPolicy(SessionCreationPolicy.STATELESS)
```

## .sessionCreationPolicy()

> 세션 **생성/사용 정책**을 지정.

- `.sessionCreationPolicy(SessionCreationPolicy.STATELESS)`
    - `STATELESS`는 **세션을 만들지도, 쓰지도 않음**
    - **JWT 토큰 기반 API**는 매 요청마다 토큰으로 인증 → 서버 메모리에 세션을 둘 필요가 없음(스케일링 유리/보안 단순화).

## .requestMatchers()

> 이 요청(경로/HTTP 메서드/조건)에 대해서는 어떤 보안 규칙을 적용하라라고 지정하는 메서드, 각각의 요청에 설정을 할 수 있다.

### 예시

```java
http.authorizeHttpRequests(auth -> auth
    .requestMatchers("/public/**").permitAll()   // 경로 패턴 매칭
    .requestMatchers(HttpMethod.GET, "/api/**").hasRole("USER") // 메서드+경로
    .anyRequest().authenticated()
);
```

---