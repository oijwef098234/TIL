### @RequirdArgsConstructor

> 자동으로 `final` 이나 `@NonNull` 의 대한 생성자를 자동으로 만들어주는 역할을 한다.

개념을 익히기 전에 `@NonNull`에 대해서 알아보자

### @NonNull이란?

> 필드에 null값을 허용하지 않도록 하는 어노테이션임

그럼 다시 본론으로 돌아와서 코드를 보자

```java
@RequiredArgsConstructor
public class MyService {
    private final MyRepository myRepository;
    private final String name;
    private int age; // final이 아님
}
```

- 다음과 같은 코드가 있다고 가정했을때 @RequiredArgsConstructor은 다음과 같은 생성자를 자동으로 생성해줄거임

```java
public MyService(MyRepository myRepository, String name) {
    this.myRepository = myRepository;
    this.name = name;
}
```

- 여기서 age가 생기지 않은 이유는 final값이 아니기 때문

지금까지 알아본걸로 보면 뭔가 @RequirdArgsConstructor이 친구는 초기값이 꼭 정해져야만하는 필드에서 생성자를 자동으로 만들어주는 느낌임

### @NoArgsConstructor

> 파라미터가 없는 기본 생성자를 자동으로 만들어줌

```java
@NoArgsConstructor
public class User {
    private String name;
    private int age;
}
```

그럼 다음과 같은 기본 생성자가 만들어진다.

```java
public User(){
}
```

여기서 기본 생성자가 왜 필요하냐 라고 생각할 수도 있는데

Been으로 등록하는 과정에서 기본 생성자는 필수임

### @AllArgsConstructor

> 모든 필드를 파라미터로 갖는 생성자를 자동으로 만들어준다.

```java
@AllArgsConstructor
public class User {
    private String name;
    private int age;
}
```

그럼 다음과 같은 생성자가 자동으로 생성된다.

```java
public User(String name, int age){
	this.name = name;
	this.age = age;
}
```

