## Facade 패턴이란?

> 여러가지 복잡한 기능을 하나에 다 때려넣고 간단해보이도록 만든것

- 예를 들면 우리가 고객센터에 전화하면 우리가 해결해야할 여러가지 문제를 고객센터에서 해결하여 우리는 문제상황만 말하면 문제가 해결되는것과 같은 간단한 느낌을 받는것이 파사드 패턴을 적용한것이다.

### 예시 코드

```java
// SubSystem1.java
public class SubSystem1 {
	public void doSomething(String name) {
    	System.out.println("동작 1: " + name);
    }
}
// SubSystem2.java
public class SubSystem2 {
	public void doSomething(String name) {
    	System.out.println("동작 2: " + name);
    }
}
// SubSystem3.java
public class SubSystem3 {
	public void doSomething(String name) {
    	System.out.println("동작 3: " + name);
    }
}
```

```java
// FacadeService.java
public class FacadeService {
    private final SubSystem1 subSystem1;
    private final SubSystem2 subSystem2;
    private final SubSystem3 subSystem3;
    
    public FacadeService() {
    	subSystem1 = new SubSystem1();
        subSystem2 = new SubSystem2();
        subSystem3 = new SubSystem3();
    }
    
    public void operate(String name) {
    	subSystem1.doSomething(name);
        subSystem2.doSomething(name);
        subSystem3.doSomething(name);
    }
}
// Client.java
public class Client {
    public static void main(String args[]){
    	FacadeService facadeService = new FacadeService();
        facadeService.operate("Client");
    }
}
```

- 이런식으로 여러개의 기능을 하나의 클래스 안에 때려박아넣는 방법을 파사드 패턴이라고 한다.