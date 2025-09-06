> 해당 어노테이션은 둘 다 JPA에서 지원하는 날짜와 관련된 어노테이션임

근데 이 두 어노테이션을 사용하려면 해야하는 것들이 잇음

### 사용 준비

```java
@SpringBootApplication
@EnableJpaAuditing        // 필수, 이 어노테이션은 엔티티를 감시하고 있다가 날짜가 자동으로 채워지도록 동작함
public class DemoApplication { … }
```

### @CreateDate

> 해당 어노테이션은 엔티티가 처음 저장된 시각을 자동으로 나타내는 어노테이션임

코드로 보면 바로 이해감

```java
package com.example.memo.entity;

import com.example.memo.dto.request.MemoRequest;
import jakarta.persistence.*;
import lombok.*;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import java.time.LocalDateTime;

@Entity
@Getter
@Setter
@Builder
@AllArgsConstructor
@NoArgsConstructor
@EntityListeners(AuditingEntityListener.class)

public class MemoEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;
    private String content;

    @CreatedDate // 이 부분을 보면 해당 필드에 값은 해당 필드가 붙어있는 엔티티가 처음 저장된 시각을 자동으로 나타내는 어노테이션이다.
    @Column(name = "date", updatable = false)
    private LocalDateTime date;

    @LastModifiedDate
    @Column(name = "lastdate")
    private LocalDateTime lastdate;

    public MemoEntity(String title, String content) {
        this.title = title;
        this.content = content;
        this.date = LocalDateTime.now();
        this.lastdate = LocalDateTime.now();
    }

    public void updateMemo(String title, String content) {
        this.title = title;
        this.content = content;
        this.lastdate = LocalDateTime.now();
    }
}
```

### @LastModifiedDate

> 이건 엔티티가 마지막으로 수정된 시각을 자동으로 저장하는 어노테이션이다.

코드를 보면 이해할 수 있디ㅏ.

```java
package com.example.memo.entity;

import com.example.memo.dto.request.MemoRequest;
import jakarta.persistence.*;
import lombok.*;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import java.time.LocalDateTime;

@Entity
@Getter
@Setter
@Builder
@AllArgsConstructor
@NoArgsConstructor
@EntityListeners(AuditingEntityListener.class)

public class MemoEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;
    private String content;

    @CreatedDate
    @Column(name = "date", updatable = false)
    private LocalDateTime date;

    @LastModifiedDate // 이 부분을 보면 해당 엔티티가 마지막으로 저장된 시각을 자동으로 저장하는 어노테이션이 lastdate라는 필드에 붙어있는것을 볼 수 있다.
    @Column(name = "lastdate")
    private LocalDateTime lastdate;

    public MemoEntity(String title, String content) {
        this.title = title;
        this.content = content;
        this.date = LocalDateTime.now();
        this.lastdate = LocalDateTime.now();
    }

    public void updateMemo(String title, String content) {
        this.title = title;
        this.content = content;
        this.lastdate = LocalDateTime.now();
    }
}
```

---

또한 위의 코드에서 `@EntityListeners(AuditingEntityListener.class)` 어노테이션은

```java
@SpringBootApplication
@EnableJpaAuditing        // 필수, 이 어노테이션은 엔티티를 감시하고 있다가 날짜가 자동으로 채워지도록 동작함
public class DemoApplication { … }
```

이렇게 적혀있는 클래스와 매핑 시켜주는 역할을 한다. → 리스너 연결