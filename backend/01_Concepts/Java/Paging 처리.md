## paging 처리란?

> 읽어들이는 값이 매우 많은경우 db에 부담을 줄이기 위해 화면에 보여지는 값들을 끊어서 읽어들이는 기법

## 개발 순서

1. Repository에서 Slice, Page 둘 중 하나로 메서드 선언
2. service에서 로직 구현
3. controller에서 값 바인딩

### 예시 코드 - Repository

```java
package com.example.gujeuck_server.domain.log.repository;

import com.example.gujeuck_server.domain.log.entity.Log;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Slice;
import org.springframework.data.jpa.repository.JpaRepository;

public interface LogRepository extends JpaRepository<Log, Long>, LogRepositoryCustom {
  Slice<Log> findAllBy(Pageable pageable);
}
```

### 예시 코드 - Service

```java
package com.example.gujeuck_server.domain.admin.service.list;

import com.example.gujeuck_server.domain.admin.dto.response.UseListResponse;
import com.example.gujeuck_server.domain.admin.facade.AdminFacade;
import com.example.gujeuck_server.domain.log.repository.LogRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Slice;
import org.springframework.data.domain.Sort;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
@RequiredArgsConstructor
public class ReadAllUseListService {
    private final LogRepository logRepository;
    private final AdminFacade adminFacade;

    @Transactional(readOnly = true)
    public Slice<UseListResponse> readAllUseList(Pageable p) {
        adminFacade.currentUser();

         Pageable pageable = PageRequest.of(
                p.getPageNumber(),
                p.getPageSize(),
                Sort.by(Sort.Direction.DESC, "visitDate")
                        .and(Sort.by(Sort.Direction.DESC, "id"))
                );

        return logRepository.findAllBy(pageable)
                .map(UseListResponse::from);
    }
}
```

### 예시 코드 - Controller

```java
    @GetMapping("/list/all")
    public Slice<UseListResponse> getAllUseList(
            @PageableDefault(size = 10, sort = {"visitDate", "id"}, direction = Sort.Direction.DESC)
            Pageable pageable) {
        return readAllUseListService.readAllUseList(pageable);
        }
```

---

## Pageable이란?

> 몇 쪽(page), 한 쪽에 몇 개(size), 어떤 정렬(sort)” 정보를 담는 페이징 요청 객체

- 따라서 이 객체를 보고 어떤 값을 얼마나 어느 시점에 어떤 기준으로 보낼지가 정해지는거임