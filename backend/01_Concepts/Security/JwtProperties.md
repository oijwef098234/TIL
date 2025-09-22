## JwtProperties란?

> JWT를 다룰 때 필요한 **설정**들을 **한 곳에 묶어 관리하는 클래스임**

## 왜 필요한가?

- **하드코딩 방지**: 코드 안에 `"mySecretKey"` 같은 걸 직접 쓰면 보안 취약 + 수정하기 어려움.
- **환경 분리**: 개발/운영 환경별로 키나 만료 시간을 다르게 가져갈 수 있음.
- **재사용/가독성**: `JwtTokenProvider` 같은 데서 쉽게 불러다 쓸 수 있음.

### 예시

```java
import lombok.Getter;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Getter
@Component
@ConfigurationProperties(prefix = "jwt")
public class JwtProperties {
    private String secret;
    private long accessTokenValidity;
    private long refreshTokenValidity;
    private String header;
    private String prefix;
}
```

---