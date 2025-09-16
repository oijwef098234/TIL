## RunTimeException이란?

> 코드 흐름·상태가 잘못됐을 때 발생하는 오류

### 예시

```java
if (count < 0) throw new RuntimeException("count는 음수면 안 돼");
```

- 아예 성립이 되지 않는 코드

---

## 예방법

> 요구조건을 선행 검사(guard)로 막기, 테스트로 케이스 커버