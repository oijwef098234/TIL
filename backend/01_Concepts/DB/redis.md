## redis란?

> **데이터를 메모리에 저장해서 미친 속도로 읽고 쓰는 NoSQL 데이터베이스**

- 다른 db들은 HDD를 거치니까 시간이 오래걸리고 느림
- 하지만 redis는 ram에 저장해서 미친듯이 빠름

|특징|설명|
|---|---|
|**In-Memory**|모든 데이터를 메모리에 저장해서 속도가 매우 빠름|
|**Key-Value 구조**|`key: value` 형태로 데이터 저장 (ex: `"user:1": "Jeongug"`)|
|**데이터 타입 다양**|String, List, Set, Sorted Set, Hash 등 여러 자료구조 지원|
|**Persistence (영속성)**|메모리 기반이지만, 설정에 따라 디스크에 백업 가능 (RDB, AOF)|
|**Pub/Sub**|채팅방, 알림 시스템 등에 쓰이는 “발행/구독” 기능 지원|
|**Atomic Operation**|여러 명령을 동시에 처리할 때도 데이터 꼬이지 않음|
|**Cluster 지원**|여러 서버에 데이터를 나눠 저장해서 확장성 뛰어남|

### 비교표

|항목|Redis|MySQL|
|---|---|---|
|**저장 위치**|메모리|디스크|
|**속도**|매우 빠름 ⚡|상대적으로 느림|
|**데이터 구조**|Key-Value|테이블(행/열)|
|**데이터 영속성**|옵션 (RDB, AOF)|기본|
|**주 용도**|캐시, 세션, 큐|영구 저장, 관계형 데이터 관리|
|**확장성**|수평 확장 쉬움|수직 확장 중심|

---

|**기술 스택**|**DB 유형**|**주요 역할 및 특징**|
|---|---|---|
|**Spring Data JPA**|관계형 DB (MySQL, Oracle, PostgreSQL 등)|Java 객체 $\leftrightarrow$ RDB 테이블 매핑 (ORM). **SQL 문법 사용**.|
|**Spring Data Redis**|비관계형 DB (Redis)|Java 객체 $\leftrightarrow$ Redis 자료구조 매핑. **Redis 명령어 사용**.|

- 그래서 JpaRepository는 접근이 불가능하다
- 따라서 CrudRepository같은것을 사용해야한다.