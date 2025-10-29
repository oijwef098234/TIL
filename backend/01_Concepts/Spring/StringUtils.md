## StringUtils란?

> 자바에서 String 클래스가 제공했던, 문자열 관련 기능들을 더욱 더 강화한 클래스

---

## hasText

### 공식 클래스 코드

```java
	@Contract("null -> false")
	public static boolean hasText(@Nullable String str) {
		return (str != null && !str.isBlank());
	}
```

- 보이는것과 같이
- null이거나
- 공백을 포함하면 True가 출력되지 않고
- null이지 않고 공백이 포함되어있지 않을대만 참이 출력된다.

---