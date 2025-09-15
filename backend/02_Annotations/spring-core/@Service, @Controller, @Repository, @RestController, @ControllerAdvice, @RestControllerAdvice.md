### @Service

> 해당 어노테이션이 붙어있는 클래스는 서비스 로직을 처리하기 위해 만들어진 클래스라는것을 spring에게 알려주기 위함

- 이 로직은 기본적인 crud를 작성하고 여러 기능들을 작성하는 파트임

### @Controller

> 해당 어노테이션이 붙어있는 클래스는 컨트롤러 로직을 처리하기 위해 만들어진 클래스라는것을 spring에게 알려주기 위함

- 이 로직은 클라이언트의 요청을 Service가 처리하기 쉽도록 처리해줌

### @Repository

> 해당 어노테이션이 붙어있는 클래스는 레포지토리에 대한 로직을 처리하기 위해 만들어진 클래스라는것을 spring에게 알려주기 위함

- 이 로직은 DB에 직접적으로 접근하여 DB를 관리하거나 쿼리를 수행하거나 sprgin이 자동으로 예외처리를 하도록 도와줌

### @RestController

> 해당 어노테이션은 `@Controller` + `@ResponseBody` 느낌이다.

- 클라이언트의 요청을 처리하는`Controller`역할도 담당하고
- 서비스에서 보내는 응다블 다시 `JSON`형태로 변환하는 `@ResponseBody` 의 역할도 한다.

### @ControllerAdvice

> 해당 어노테이션은 예외처리를 하는 controller를 명시하는 어노테이션이다.

### @RestControllerAdvice

> **전역(글로벌)으로 REST 컨트롤러에 적용되는 보조(Advice) 클래스**를 만들 때 쓰는 어노테이션

- `@ControllerAdvice + @ResponseBody`의 합성임
- **핸들러 메서드의 반환값이 자동으로 JSON**으로 응답

---