아래에서 말한 모든 어노테이션은 Controller에서 사용하는 어노테이션임

한마디로 클라이언트의 요청 처리에 관련한 어노테이션임

### @PostMapping

> 클라이언트에게서 `Post` 의 요청이 왔을때의 관련 처리에 관련한 코드라는 것을 명시

```java
    @PostMapping("/create") // 생성
    public void createPost(@RequestBody PostRequest postRequest) {
        createService.createPost(postRequest);
    }
```

### @GetMapping

> 클라이언트에게서 `Get` 의 요청이 왔을때의 관련 처리에 관련한 코드라는 것을 명시

```java
    @GetMapping("/all") // 전체 조회
    public List<PostResponse> getPostRepository() {
        return readService.getPostRepository();
    }

    @GetMapping("/{id}") //. 아이디조회
    public PostResponse findById(@PathVariable Long id) {
        return readService.findById(id);
    }
```

### @DeletMapping

> 클라이언트에게서 `Delete` 의 요청이 왔을때의 관련 처리에 관련한 코드라는 것을 명시

```java
    @DeleteMapping("/{id}") // 삭제
    public void deleteById(@PathVariable Long id) {
        deleteService.deletePost(id);
    }
```

### @PatchMapping

> 클라이언트에게서 `Patch` 의 요청이 왔을때의 처리에 관련한 코드라는 것을 명시

```java
    @PatchMapping("/{id}") // patch는 부분적인 수정에 관련한 클라이언트 요청임
    public void updatePost(@RequestBody PostRequest postRequest, @PathVariable Long id) {
        updateService.updatePost(postRequest, id);
    }
```

### @PutMapping

> 클라이언트에게서 `Put` 의 요청이 왔을때의 처리에 관련한 코드라는 것을 명시

```java
		@PutMapping("/{id}") // put은 전체 수정에 관련한 클라이언트 요청임
		public void replacePost(@RequestBody PostRequest postRequest, @PathVariable Long id) {
		    updateService.replacePost(postRequest, id);
		}
```