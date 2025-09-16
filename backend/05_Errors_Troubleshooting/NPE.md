## NPE - NullPointException란?

> null을 객체처럼 썼을 때, null값이 발생했을때

### 예시

```java
String name = null;
int len = name.length(); // NPE
```

---

## 예방법

- **사전 체크**: `Objects.requireNonNull(name, "name 필요")`
- 기본값 주기: `String nick = Optional.ofNullable(nick).orElse("guest");`
- 설계에서 가능한 **널 미허용**(생성자에서 필수값 강제), `@NotNull/@NonNull` 사용.