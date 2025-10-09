## @PrePersist란?

> DB에 엔티티가 처음 저장되기 전에 자동으로 실행되는 메서드에 붙이는 어노테이션

### 예시 코드

```java
package com.example.gujeuck_server.domain.log.entity;

import com.example.gujeuck_server.domain.purpose.entity.Purpose;
import com.example.gujeuck_server.domain.user.entity.User;
import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

import java.time.LocalDate;

@Entity
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class Log {

    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "purpose_id", nullable = false)
    private Purpose purpose;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "leader_id", nullable = false)
    private User leader;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id")
    private User companion;

    @Column(name = "male_count", nullable = false)
    private int maleCount;

    @Column(name = "female_count", nullable = false)
    private int femaleCount;

    @Column(name = "visit_date", nullable = false)
    private LocalDate visitDate;

    @PrePersist
    public void prePersist() {
        if(visitDate == null) {
            visitDate = LocalDate.now();
        }
    }
}
```

- 지금 위의 코드에서 @PrePersist가 붙어있는 메서드가 실행되면
- visitDate필드가 자동으로 설정되거나 따로 사용자가 설정할 수 있다.