## @Indexed란?

> id처럼 주 키 대신 조회할 수 있게 해주는 보조 키

필요한 이유는 단지 id외의 필드로 조회가 가능하도록 하기 위해

### 예시 코드

```java
package com.example.springsecurity.domain.user.entity;

import jakarta.persistence.Id;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;
import org.springframework.data.redis.core.RedisHash;
import org.springframework.data.redis.core.TimeToLive;
import org.springframework.stereotype.Indexed;

@RedisHash
@Builder
@Getter
@NoArgsConstructor
@AllArgsConstructor
public class RefreshToken {
    @Id
    private String password;

    @Indexed
    private String token;

    @TimeToLive
    private Long timeToLive;
}
```

보이는것과 같이 token이 id대신 조회될 수 있음을 명시해두는 것이다.