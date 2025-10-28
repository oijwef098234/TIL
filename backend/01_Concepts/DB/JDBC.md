## JDBC란?

> 자바 언어가 데이터베이스에 접속하고 데이터를 관리하는 **기술 표준** 및 **전체 아키텍처**를 총칭하는 광범위한 개념

## JDBC API란?

> 자바 개발자가 데이터베이스 작업을 수행하기 위해 **직접 사용하는 프로그래밍 명령어**

- JDBC 안에 하위 개념
- JDBC의 **핵심**. `java.sql` 패키지에 있는 **인터페이스와 클래스 집합**입니다.

### 없을때

|**작업**|**MySQL 데이터베이스 (MySQL 전용 API 가정)**|**Oracle 데이터베이스 (Oracle 전용 API 가정)**|
|---|---|---|
|**1. DB 접속 요청**|`MySQL_Client.establishConnection("host:port", "user", "pass")`|`Oracle_DBLinker.openSession("TNS_NAME", "schema", "auth_key")`|
|**2. SQL 실행 요청**|`MySQL_Connection.runQuery("SELECT * FROM member")`|`Oracle_Session.createCursor().executeSql("SELECT * FROM member")`|
|**3. 결과 탐색 요청**|`MySQL_Result.fetchNextRow()`|`Oracle_Cursor.moveToNext()`|

### 있을때

|**작업**|**MySQL 데이터베이스**|**Oracle 데이터베이스**|
|---|---|---|
|1. DB 접속 요청|`DriverManager.getConnection("jdbc:mysql://...")`|`DriverManager.getConnection("jdbc:oracle:thin://...")`|
|2. SQL 실행 요청|`Connection.createStatement().executeQuery("SELECT * FROM member")`|`Connection.createStatement().executeQuery("SELECT * FROM member")`|
|3. 결과 탐색 요청|`ResultSet.next()`|`ResultSet.next()`|

위와 같이 각각 다른 db들의 접근 방식이나 각기 다른 코드들을 하나의 표준형태로 맞춰놓아

접근이 보다 쉽게 가능하게 만든것이 JDBC이다.