## UserDetalis란?

> Spring Security에 맞는 방식으로 클라이언트 정보를 표시하는 기본 인터페이스

- db에서 불러온 사용자의 형태든 정보든 다 제각각임
- 그래서 그런 것들을 Security가 쓸 수 있도록 맞춰줌

### 예시코드

```java
public interface UserDetails {
    String getUsername();      // 로그인 식별자 (ex. 이메일)
    String getPassword();      // 암호화된 비밀번호
    Collection<? extends GrantedAuthority> getAuthorities(); // 권한 목록

    boolean isAccountNonExpired();     // 계정 만료 여부
    boolean isAccountNonLocked();      // 계정 잠김 여부
    boolean isCredentialsNonExpired(); // 비밀번호 만료 여부
    boolean isEnabled();               // 계정 활성화 여부
}
```

```java
@Getter
@AllArgsConstructor
public class CustomUserDetails implements UserDetails { // 재정의

    private final UserEntity user;

    @Override
    public String getUsername() {
        return user.getExternalId(); // 또는 user.getEmail()
    }

    @Override
    public String getPassword() {
        return user.getPassword();   // 암호화된 비밀번호
    }

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return List.of(new SimpleGrantedAuthority("ROLE_USER")); // 권한 부여
    }

    @Override
    public boolean isAccountNonExpired() { return true; }

    @Override
    public boolean isAccountNonLocked() { return true; }

    @Override
    public boolean isCredentialsNonExpired() { return true; }

    @Override
    public boolean isEnabled() { return true; }
}
```

---

## UserDetailsService란?

> 사용자가 로그인을 시도할 때 db에서 정보를 꺼내와 UserDetali로 전달하는 역할을 함

```java
public interface UserDetailsService {
    UserDetails loadUserByUsername(String username) throws UsernameNotFoundException;
}
```

```java
@Service
@RequiredArgsConstructor
public class CustomUserDetailsService implements UserDetailsService {

    private final UserRepository userRepository;

    @Override
    public UserDetails loadUserByUsername(String externalId) throws UsernameNotFoundException {
        UserEntity user = userRepository.findByExternalId(externalId)
                .orElseThrow(() -> new UsernameNotFoundException("사용자를 찾을 수 없음: " + externalId));

        return new CustomUserDetails(user);
    }
}
```