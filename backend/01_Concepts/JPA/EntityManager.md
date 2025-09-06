> jpa에서 entity와 DB간의 모든 역할을 처리하는 객체이다.

크게 EntityManager는 4가지의 역할을 할 수 있다.

1. 엔티티 저장
2. 조회
3. 삭제
4. 수정

### 엔티티 저장 - `persist()`

```java
EntityManager em = ...;
EntityTransaction tx = em.getTransaction();
tx.begin(); // 트랜잭션 시작

User user = new User("홍길동", "hong@example.com");
em.persist(user); // INSERT 실행됨

tx.commit(); // 트랜잭션 커밋 → DB 반영
```

여기서 굳이 트렌젝션이 필요한가? 라는 의문이 들 수 있다.

근데 persist는 트렌젝션이 완료되고 커밋이 되며 insert가 된다.

### 조회 - `find()`

```java
User foundUser = em.find(User.class, 1L);
System.out.println(foundUser.getName());
```

여기서 `User foundUser = em.find(User.class, 1L);` 이 코드는 User클래스에서 아이디의 형태가 Long인 1인 아이디를 찾아서 foundUser에 저장해라

라는 의미를 가지고 있다.

### 삭제 - `remove()`

```java
User user = em.find(User.class, 1L);
em.remove(user);
```

여기선 user라는 객체 안에 User라는 클래스에서 타입이 Long인 1인 아이디를 찾아서 저장한뒤

그 값을 지우는 것이다.

### 수정 - `merge()`

```java
User user = em.find(User.class, 1L);
user.setName("이순신");
em.merge(user); // 트랜잭션 커밋 시 UPDATE 실행
```

여기서도 그냥 user안에 아이디아 1인것을 저장하고

그걸 수정하는 메서드를 통해서 이순신으로 바꾼 뒤

UPDATE를 하는것이다.

---

여기서 알 수 있는것은 무엇이냐면

뭔가 변경사항이 발생하는 경우에는 트렌젝션을 이용하여 그 변경사항이 중간에서 멈추지 않도록 하면서

또한 커밋이 완료된 후 저장이 될 수 있도록한다.