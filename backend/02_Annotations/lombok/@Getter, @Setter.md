### @Getter

> 필드의 값을 외부에서 가져올 수 있게 하는 메서드를 만들어주는 친구임

```java
import lombok.Getter;
import lombok.Setter;

public class User {
    private String name;
    private int age;
}
```

- 이 코드를 보면 name과 age가 private로 되어잇는것을 볼 수 있다.
- 그럼 이런 값들은 외부에서 어떻게 불러오고 사용할까?
- 그럴때 필요한게 @Getter이다.

```java
import lombok.Getter;
import lombok.Setter;

@Getter
public class User {
    private String name;
    private int age;
}
```

- 이렇게 적어주면

```java
public String getName(){
	return name;
}

public int getAge(){
	return age;
}
```

- 이런 메소드를 자동으로 생성해주어 외부에서 메소드를 통해 값을 사용할 수 있게 해준다.

근데 여기서 의문이 생길 수 있다 사용을 해야하는데 값을 바꿔야하는 상황이 생기면 return만 하는 메소드로 모든걸 다 할 수 없는데 어떻게 해야할까?

그럴때 필요한게 Setter이다.

### @Setter

> 외부에서 필드 값을 변경할 수 있는 메서드를 만들어주는 역할을 한다.

```java
import lombok.Getter;
import lombok.Setter;

public class User {
    private String name;
    private int age;
}
```

- 아까와 같은 코드가 있다고 가정했을때 외부에서 값을 변경하려면 어떻게 해야할까?

```java
import lombok.Getter;
import lombok.Setter;

@Setter
public class User {
    private String name;
    private int age;
}
```

- 이렇게 바꿔주면 자동으로

```java
public void setName(String name){
	this.name = name;
}

public void getAge(int age){
	this.age = age;
}
```

- 이런 메서드가 자동으로 생성된다.