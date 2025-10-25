## @RedisHash란?

> 해당 어노테이션이 붙은 객체가 저장될때 해시의 형태로 저장이 된다.

### 해시란?

> 하나의 필드에 모든 필드를 연관지어서 저장

|**자료구조**|**저장 방식**|**비고**|
|---|---|---|
|**일반 문자열 (Key-Value)**|`user:100:name` -> "Alice"||
|`user:100:email` -> "[alice@a.com](mailto:alice@a.com)"|필드마다 새로운 Key를 사용해 **메모리 오버헤드 증가**||
|**Hash 자료구조**|`user:100` (Key) -> { `name`: "Alice", `email`: "[alice@a.com](mailto:alice@a.com)" }|하나의 Key 아래에 모든 필드를 묶어 **메모리 절약 및 성능 향상**|

### 예시 코드 - RefreshToken

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

- 우선 테이블의 이름을 `@RedisHash(”my_table”)` 저장을 하는데, 만약에 그냥 아무것도 안적으면 클래스명으로 들어가게 된다.
    
- 우선 db에 저장되는 방식부터 알아보자.
    
- **데이터 저장 (Hash):**
    
    `HGETALL RefreshToken:admin123 -> { "token": "abcxyz...", "timeToLive": "604800" }`
    
- **만료 시간 설정 (TTL):**
    
    `TTL RefreshToken:admin123 -> 604800 (초 단위로 남은 시간)`
    
- **인덱스 저장 (Set/Sorted Set):**
    
    `SMEMBERS RefreshToken:token:abcxyz... -> {"admin123"}`
    

---

따라서 `@Id, @Indexed, @TimeToLive`의 어노테이션을 잘 사용해야한다.