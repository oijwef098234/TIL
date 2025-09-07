# Stream 파이프라인  .stream() → .map(…) → .toList()

## 개념 요약

- **Stream**: 컬렉션/배열 등의 요소를 “흐름”으로 다루는 API.
- **중간 연산**: `map`, `filter`, `sorted` 등 — **게으름(lazy)**, 바로 실행되지 않음.
- **종단 연산**: `toList()`, `collect`, `forEach`, `sum` 등 — 이때 **실행됨**.

---

## 단계별 요약

1. `.stream()`
    - 배열과 같은 요소들을 순차 스트림으로 변환
2. `.map(fn)`
    - **요소 1개 → 다른 값 1개**로 변환(타입 변경 가능).
3. `.toList()`
    - **종단 연산**: 파이프라인이 이 시점에 실제로 실행되어 **리스트**를 만든다.

---

## 예시 코드 1

```java
List<String> upper = names.stream()
    .map(String::toUpperCase)
    .toList(); // 불변
```

위의 코드는 소문자 문자열 리스트를 대문자 문자열 리스트로 변환하는 코드이다.

여기서 :: 가 뭔지 의문이 생길 수 있다.

### :: 가 뭘까?

> `::` 는 **람다를 더 짧게 쓰는 문법이다.**

`s -> s.toUpperCase()`와 동일한 의미를 가진다.

---

## 예시 코드 2

```java
record PostDto(Long id, String title) {}

List<PostDto> dtos = posts.stream()
    .map(p -> new PostDto(p.getId(), p.getTitle()))
    .toList();
```

위의 코드는 posts라는 엔티티 배열 안에 있는 값을 PostDto라는 자료형인 배열에 하나하나 다시 Entity → DTO형태로 바꾸는 코드이다.

---