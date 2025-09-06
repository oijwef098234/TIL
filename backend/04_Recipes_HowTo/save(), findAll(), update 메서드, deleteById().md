## save(), findAll(), `update 메서드`, `deleteById()` 은 CRUD개발을 할 때 각 기능에 사용되는 핵심 개념이다.

그럼 우선 각각의 기능을 알아보기 위해서 예시 코드로 알아보자

---

### save()

> save()는 새로운 엔티티를 저장하는데 사용한다.

예를 들어서 내가 메모장 CRUD를 만들었다고 가정해보자

```java
package com.example.memo.service;

import com.example.memo.dto.request.MemoRequest;
import com.example.memo.dto.response.MemoResponse;
import com.example.memo.entity.MemoEntity;
import com.example.memo.repository.MemoRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
@RequiredArgsConstructor
public class CreateMemo {
    private final MemoRepository memoRepository;

    @Transactional
    public MemoResponse createMemo(MemoRequest memoRequest) {
        MemoEntity memoEntity = new MemoEntity(memoRequest.getTitle(), memoRequest.getContent());
        MemoEntity savedMemoEntity = memoRepository.save(memoEntity); // 여기서 새롭게 생성된 메모를 하나의 엔티티로 인식하여 INSERT를 수행함
        return new MemoResponse(savedMemoEntity);
    }
}
```

여기서 save()의 작동 원리는

1. if(id == null) → 새 엔티티를 영속 컨텍스트에 **등록(INSERT 예약)**
2. if(id ≠ null) → 영속 컨텍스트 1차 캐시에 없는지 확인 → 있으면 더티체킹, 없으면 `merge()`

---

### findAll()

> 말 그대로 다 찾아서 리스트로 변환하는거임

코드를 통해 살펴보면 조금 더 쉬움

```java
package com.example.memo.service;

import com.example.memo.dto.response.MemoResponse;
import com.example.memo.entity.MemoEntity;
import com.example.memo.repository.MemoRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.util.ArrayList;
import java.util.List;
import java.util.Optional;

@Service
@RequiredArgsConstructor
public class ReadMemo {
    private final MemoRepository memoRepository;

    @Transactional(readOnly = true)
    public List<MemoResponse> ReadAllMemo() { // 이 클래스를 집중해서 보세용
        List<MemoEntity> memoEntities = memoRepository.findAll(); // 여기 보면 findAll()이 있는데 이게 말 그대로 MemoEntity의 형태로 된 필드들을 memoEntity안에 리스트의 형식으로 전부 다 찾아서 넣는것임

        List<MemoResponse> memoResponses = new ArrayList<>();

        for (MemoEntity memoEntity : memoEntities) {
            memoResponses.add(new MemoResponse(memoEntity));
        }
        return memoResponses;
    }

    @Transactional(readOnly = true)
    public MemoResponse ReadMemoById(Long id) {
        MemoEntity memoEntity = memoRepository.findById(id).orElseThrow(
                () -> new IllegalArgumentException("해당 id에 맞는 메모가 없습니다")
        );
        return new MemoResponse(memoEntity);
    }
}
```

---

### update() 메서드

> 이게 뭐냐면 entity 필드 값들을 업데이트 하는 메서드이다

다음 코드를 통해 알아보자

### update클래스

```java
package com.example.memo.service;

import com.example.memo.dto.request.MemoRequest;
import com.example.memo.dto.response.MemoResponse;
import com.example.memo.entity.MemoEntity;
import com.example.memo.repository.MemoRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
@RequiredArgsConstructor
public class UpdateMemo {
    private final MemoRepository memoRepository;

    @Transactional
    public MemoResponse updateMemo(Long id, MemoRequest dto) {
        MemoEntity memo = memoRepository.findById(id).orElseThrow(
                () -> new IllegalArgumentException("해당 id에 맞는 메모가 없습니다")
        );

        memo.updateMemo(dto.getTitle(), dto.getContent()); // 여기보면 갑자기 update라는 메서드가 등장한다, 이게 바로 entity에 정의되어있다.

        return new MemoResponse(memo);
    }
}
```

### Entity 클래스

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
        this.content = content;
        this.lastdate = LocalDateTime.now();
    }
}
```

이 코드를 보면 한가지의 의뭉니 생긴다.

> 왜 update메서드를 serivce에 정의하지 않고 entity에 정의했을까?

그 이유는 간단하다.

자기의 필드 변화는 자기 자신이 책임져야하기 때문에

entity에서 나온 필드인만큼 변화가 일어나는 핵심과정인 update는 entity가 책임을 지는게 맞다.

---

### deleteById()

> 말 그대로 id로 필드를 식별하여 삭제하는 것이다.

코드를 보면서 이해하자

다음은 메모 CRUD에서 delete부분이다.

```java
package com.example.memo.service;

import com.example.memo.repository.MemoRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
@RequiredArgsConstructor
public class DeleteMemo {
    private final MemoRepository memoRepository;

    @Transactional
    public void deleteMemo(Long id) {
        if(!memoRepository.existsById(id)){
            throw new IllegalArgumentException("해당 아이디에 맞는 메모 없음");
        }
        memoRepository.deleteById(id); // 이 부분을 잘 보자
    }
}
```

주석 처리를 한 부분이 핵심 로직인데 저기서 deleteById를 이용해서 괄호 안에 있는 id를 가진 필드를 지운다.
