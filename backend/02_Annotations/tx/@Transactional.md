> 하나라도 실패하면 전부 실패, 전부 다 성공해야지 성공인 하나의 작업 단위임

이게 무슨말이냐면

작업을 실행하는도중 작업이 성공적으로 완료되지도 않았는데 중간에서 예외 상황이 발생하면, 아예 처음부터 다시 시작함, 그래서 완전히 그 작업이 끝나기 전까지는 완료가 되지 않음

트렌젝션을 하려면 아래의 네가지가 보장되어야만 함

| 이름          | 설명                      |
| ----------- | ----------------------- |
| **A** (원자성) | 작업은 모두 성공하거나, 모두 실패해야 함 |
| **C** (일관성) | 작업 전후의 DB 상태가 일관돼야 함    |
| **I** (격리성) | 다른 작업과 충돌하지 않아야 함       |
| **D** (지속성) | 작업이 성공하면, DB에 영구 반영돼야 함 |

보통 트렌젝션을 하나의 작업단위 안에서 모든 db의 작업을 묶어서 처리하기 위해 사용한다.

---

예시를 보면

```java
@Service
@RequiredArgsConstructor
public class BankService {
    private final AccountRepository accountRepository;

    @Transactional
    public void transfer(Long fromId, Long toId, int amount) {
        Account from = accountRepository.findById(fromId).orElseThrow();
        Account to = accountRepository.findById(toId).orElseThrow();

        from.withdraw(amount); // 출금
        to.deposit(amount);    // 입금

        // 중간에 예외 발생하면 → 둘 다 취소됨!
    }
}
```

- 이렇게 출금과 입금을 하는 과정에서 중간에 예외가 발생하면 돈은 전송이 되지 않아야한다.
- 하지만 트렌젝션을 사용하지 않으면 돈을 전송하지도 않았는데 전송이되는 대참사가 발생할 수도 있다.

근데 이 트렌젝션에도 여러 사용법이 있다.

|`@Transactional(readOnly = true)`|읽기 전용 (성능 최적화)|
|---|---|
|`@Transactional(rollbackFor = ...)`|특정 예외 발생 시 롤백 설정 가능|

하지만 이 방법을 알아보기 전에 먼저 선행되어야할 개념인 영속성 컨텍스트라는 개념을 알아보자

### 영속성 컨텍스트

> 엔티티 객체를 보관하고 있는 메모리 공간

근데 이게 도대체 뭐고 왜 필요한지 아직은 잘 모를 수 있다.

일단은 왜 필요한지부터 설명하면

- 기존에 있던 객체를 보관하면 변경사항이 발생했을떄
- 어떤 변경사항이 발생했는지
- 뭐가 바뀌었는지같은 정보를 알 수 있다.
- 또한 조회하는 서비스에서 이미 조회를 했는데 다시 조회를 하면 지연시간이 발생한다.
- 따라서 이미 조회한 객체를 메모리에 저장함으로써 다시 조회하기 전에 한번만 확인하면 다시 db에 접근 할 필요가 없어지는 것이다.

---

다시 본론으로 돌아가서

### `@Transactional(readOnly = true)`

> 조회만 하는 코드

근데 그럼 트렌젝션이 왜 사용되는거냐, 굳이 조회만 할건데 그냥 안쓰면 되는거 아님?

이라고 생각할수도 있는데

아까 말했듯이 조회를 하더라도 다시 db에 접근하는 것을 방지하기 위해서 사용함

### `@Transactional(rollbackFor = ...)`

> spring에는 여러가지 예외 상황이 있음, 하지만 트렌젝션은 런타임에러만 롤백하는데 다른 에러까지 다 롤백하는 조건을 달아주는거임

예를 들어서 내가 SQLException예외까지 롤백하고 싶으면 이런 식으로 작성하는거임

```java
@Transactional(rollbackFor = SQLException.class)
public void updateData() throws SQLException {
    // DB 작업 수행
    throw new SQLException("에러 발생"); // 👉 롤백됨!
}

```
