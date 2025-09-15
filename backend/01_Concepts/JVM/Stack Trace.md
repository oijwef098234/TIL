## Stack Trace란?

> 예외가 발생했을 때 그 시점의 호출 스택(메서드 호출 경로)을 글로 풀어 보여주는 것

---

## 작동 방식

- 프로그램이 함수를 호출할 때마다 **스택 프레임**(메서드 이름, 파일, 라인, 로컬 변수 등 일부 메타)이 쌓입니다.
- 예외가 던져지면 JVM이 **현재 스택을 위에서부터 아래까지** 펼쳐서 “어떤 메서드들이 어떤 순서로 호출되다 실패했는가”를 줄줄이 출력

```java
java.lang.NullPointerException: Cannot invoke "String.length()" because "name" is null
    at com.example.user.UserService.create(UserService.java:42)      // ① 실제로 터진 지점
    at com.example.user.UserController.create(UserController.java:27) // ② 위 메서드를 호출한 곳
    at org.springframework.web.method...                              // ③ 프레임워크 내부 호출
Caused by: java.sql.SQLIntegrityConstraintViolationException: Duplicate entry 'abc' for key 'UK_email'
    at com.mysql.cj.jdbc...                                           // ④ 근본 원인(체인)
```

이게 그 줄줄이 출력의 예시이다.

---