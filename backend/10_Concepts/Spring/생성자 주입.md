> 생성자를 통해 의존성을 주입하는 것을 말한다.

우선 생성자 주입이 어떤 식으로 이루어지는지 코드로 알아보자.

### Engine.java 클래스

```java
public interface Engine { // 엔진이 기본적으로 start라는 메서드를 가지고 있다고 명시함
    void start();
}
```

### GasolineEngine.java

```java
import org.springframework.stereotype.Component;

@Component
public class GasolineEngine implements Engine { // 여기서 가솔린 엔진 생성
    @Override // 상속을 표현한 어노테이션
    public void start() {
        System.out.println("가솔린 엔진이 켜졌습니다!");
    }
}
```

### ElectricEngine.java

```java
import org.springframework.context.annotation.Primary;
import org.springframework.stereotype.Component;

@Component
@Primary // 이 어노테이션은 여러 bean이 있을때 기본으로 주입될 클래스를 지정해주는 역할을 한다.
public class ElectricEngine implements Engine { // 여기서 전기 엔진 생성
    @Override
    public void start() {
        System.out.println("전기 엔진이 켜졌습니다!");
    }
}
```

### Car.java → 생성자 주입

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class Car {

    private final Engine engine; // 여기서 final인 이유는 한번 정해진 의존성은 바뀌면 안되기 때문이다. 

    // 생성자 주입, 여기서 방금 위에서 만든 엔진을 정의하는거임
    public Car(Engine engine) { // 이렇게 하면 이 엔진 안에 아까 기본으로 설정했던 전기 엔진이 들어가게 된다.
        this.engine = engine;
    }

    public void drive() {
        engine.start();
        System.out.println("자동차가 달립니다!");
    }
}
```

### MainApp.java (Spring Boot Application)

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

@SpringBootApplication
public class MainApp {

    public static void main(String[] args) {
        ApplicationContext context = SpringApplication.run(MainApp.class, args);

        Car car = context.getBean(Car.class); // 객체 생성
        car.drive(); // 객체를 사용하여 drive라는 메서드를 호출함
    }
}
```