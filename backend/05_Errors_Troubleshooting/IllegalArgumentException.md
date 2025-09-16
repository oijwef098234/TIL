## IllegalArgumentException란?

> 아예 잘못된 파라미터(인자)값 이 들어왔을때 발생하는 오류

### 예시

```java
public void setAge(int age) {
  if (age < 0 || age > 150) throw new IllegalArgumentException("age는 0~150");
}
```

---

## 예방법

> 메서드 입구에서 값 검증, `Objects.requireNonNull(x, "x 필요")`, 스프링이면 `@Valid` 사용.