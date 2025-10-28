## REST란?

> 자원을 이름으로 구분하여 해당 자원의 상태를 주고받는 모든 것을 의미

### 예시

|**자원 URI**|**설명**|
|---|---|
|`/users`|전체 사용자 리소스를 나타내는 컬렉션|
|`/users/{id}`|특정 사용자 리소스 하나를 나타내는 항목 (여기서 `{id}`는 사용자의 고유 식별자)|

- 위와 같이 자원을 이름으로 구분하여 데이터나 상태를 주고 받는 모든것

### **즉 REST란**

1. HTTP URI(Uniform Resource Identifier)를 통해 자원(Resource)을 명시하고,
2. HTTP Method(POST, GET, PUT, DELETE, PATCH 등)를 통해
3. 해당 자원(URI)에 대한 CRUD Operation을 적용하는 것을 의미

## 왜 사용하냐?

### 1. 직관성

- 자원을 이름으로 구분하여 보다 쉽게 이해하고 직관적이고 일관적이다.

### 2. 일관성

- HTTP 프로토콜의 인프라를 그대로 사용하므로 REST API 사용을 위한 별도의 인프라를 구출할 필요가 없다.
- 이게 무슨 말이냐면
- 만약 사용하지 않게 된다면

### ~~예시: SOAP 또는 커스텀 프로토콜 사용~~

### ~~1. 새로운 통신 규약/포맷의 필요성~~

- **~~REST가 아닌 경우**: 클라이언트와 서버 간의 데이터 전송에 HTTP가 아닌 **다른 전용 프로토콜**을 사용하거나, HTTP를 사용하더라도 **JSON/XML이 아닌 매우 복잡하고 커스터마이징된 메시징 포맷**을 강제하는 경우.~~
- **~~필요한 별도 인프라**:~~
    - **~~전문 클라이언트 라이브러리**: 클라이언트가 서버와 통신하려면 해당 프로토콜을 해석하고 메시지 포맷을 생성/파싱할 수 있는 **전용 소프트웨어 라이브러리**를 반드시 설치해야 합니다. 웹 브라우저만으로는 통신이 불가능합니다.~~
    - **~~중간 처리기(Middleware)**: 일반적인 웹 프록시나 캐시 서버가 이 전용 메시지 포맷을 이해하지 못하므로, 메시지를 검증하거나 변환해 주는 **새로운 미들웨어 서버**를 통신 경로에 추가로 구축해야 할 수 있습니다.~~

### ~~2. 캐싱 및 로드 밸런싱의 어려움~~

- **~~REST가 아닌 경우**: 요청 메시지가 복잡하고 매번 달라져 HTTP의 **GET/POST 구분이 모호**하거나, **상태(State)를 서버에 저장**하도록 설계된 경우.~~
- **~~필요한 별도 인프라**:~~
    - **~~캐싱 포기**: 웹의 가장 큰 장점인 **HTTP 캐싱** 기능을 활용할 수 없게 됩니다. 모든 요청이 무조건 서버에 도달해야 하므로, 서버와 네트워크 부하가 증가합니다.~~
    - **~~세션 유지 로드 밸런서**: 무상태성이 보장되지 않으면, 특정 클라이언트의 요청은 항상 **같은 서버**로 가도록 보장해야 합니다(세션 유지). 이는 로드 밸런서(부하 분산 장치)를 훨씬 복잡하게 설정하고 관리해야 함을 의미하며, 서버 확장성을 저해합니다.~~
- 이런 불편함이 생길 뿐더러
- 1 ㄷ 1매핑이 아닌 다 각기 다른 통신 방법이나 구분하는방법을 숙지하고 공부해야하는 불편함이 생긴다.

---

## REST API란?

> **RESPT API란 REST의 원리를 따르는 API를 의미**

- 웹의 기본 프로토콜인 **HTTP를 최대한 활용**하여 클라이언트(웹, 앱)와 서버가 네트워크를 통해 데이터를 주고받는 방식의 표준

### **REST API 설계 예시**

**1. URI는 동사보다는 명사를, 대문자보다는 소문자를 사용하여야 한다.**

- Bad Example : [http://khj93.com/Running/](http://khj93.com/Running/)
- Good Example :  [http://khj93.com/run/](http://khj93.com/run/)

**2. 마지막에 슬래시 (/)를 포함하지 않는다.**

- Bad Example : [http://khj93.com/test/](http://khj93.com/test/)
- Good Example :  [http://khj93.com/test](http://khj93.com/test)

**3. 언더바 대신 하이폰을 사용한다.**

- Bad Example : [http://khj93.com/test_blog](http://khj93.com/test_blog)
- Good Example :  [http://khj93.com/test-blog](http://khj93.com/test-blog)

**4. 파일확장자는 URI에 포함하지 않는다.**

- Bad Example : [http://khj93.com/photo.jpg](http://khj93.com/photo.jpg)
- Good Example :  [http://khj93.com/photo](http://khj93.com/photo)

**5. 행위를 포함하지 않는다.**

- Bad Example : [http://khj93.com/delete-post/1](http://khj93.com/delete-post/1)
- Good Example :  [http://khj93.com/post/1](http://khj93.com/post/1)