## CrudRepository란?

> **기본 CRUD Create,Read,Update,Delete** 작업을 처리하기 위한 인터페이스

### 주요 메서드

|메소드|설명|
|---|---|
|save(S entity)|엔티티를 저장하거나 업데이트|
|findBy(ID, id)|ID로 엔티티를 조회|
|findAll()|모든 엔티티를 조회|
|count()|전체 엔티티 수를 반환|
|deleteById(ID, id)|ID로 엔티티를 삭제|
|delete(T entity)|주어진 엔티티를 삭제|
|deleteAll()|모든 엔티티를 삭제|

- JpaRepository보다 하위개념인데 왜 이걸 사용하냐

### 사용 예시

```java
package com.example.gujeuck_server.domain.user.repository;

import com.example.gujeuck_server.domain.admin.entity.RefreshToken;
import org.springframework.data.repository.CrudRepository;
import org.springframework.stereotype.Repository;

import java.util.Optional;

@Repository
public interface RefreshTokenRepository extends CrudRepository<RefreshToken, String> {

    Optional<RefreshToken> findByToken(String token);
}
```

- 위 코드와 같이 db가 아닌 redis같은것을 이용하면 CrudRepository를 상속받기도 한다.