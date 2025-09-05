여기있는 명령어들을 크게 분류하자면 크게 CRUD로 구분 할 수 있다.

### SELECT - READ

> 적어둔 그대로 DB에 있는 값을 읽어오는 역할을 한다.

```sql
SELECT name, email FROM users;
// name과 email을 users에서 읽어와라
```

여기서 잘 보면 select는 `select from` 의 형식으로 사용된다.

### INSERT - CREATE

> 적어둔 그대로 DB에 새로운 값을 삽입, 즉 추가한다.

```sql
INSERT INTO users (name, email) VALUES ('홍길동', 'hong@example.com');
// users 테이블에 있는 name, email값을 홍길동과 hong@example.com로 각각 매핑하고 하나의 row를 추가해라
```

여기서 잘 보면 INSERT는 `INSERT VALUE` 의 형식으로 사용된다.

### UPDATE - UPDATE

> 이것또한 말 그대로 그냥 DB에 있는 값을 업데이트, 즉 수정하는 거임

```sql
UPDATE users SET email = 'newemail@example.com' WHERE name = '홍길동';
// users테이블에 있는 name이 홍길동인 사람의 email을 newemail@example.com로 바꿔라
```

여기서 잘 보면 UPDATE는 `UPDATE SET WHERE` 의 형태로 사용된다.

_**근데 여기서 WHERE을 빼먹으면 테이블 전체가 수정됨으로 주의하셈**_

### DELETE - DELETE

> 이것또한 적혀있는 그대로 DB에 있는 값을 지우는 명령어임

```sql
DELETE FROM users WHERE name = '홍길동';
// users테이블에 name이 홍길동인 사람의 row를 지워라
```

여기서 잘 보면 DELETE는 `DELETE SET WHERE` 의 형태로 사용된다.

_**근데 여기서 WHERE을 빼먹으면 테이블 전체가 수정됨으로 주의하셈**_
