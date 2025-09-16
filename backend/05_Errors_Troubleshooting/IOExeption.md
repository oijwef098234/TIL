## IOException이란?

> 입출력에 있어서 발생하는 모든 오류

- 파일이 없음/권한 없음/디스크 꽉 참
- 네트워크 끊김/시간초과/상대 서버 죽음

### 예시

```java
List<String> lines;
try (var br = Files.newBufferedReader(Path.of("config.txt"))) {
  lines = br.lines().toList();
} // 파일이 없거나 권한 없으면 IOException
```

---

## 예방법

> 존재/권한 사전 확인, 네트워크는 재시도·타임아웃 설정, `try-with-resources`로 자원 정리.