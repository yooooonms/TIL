# @PathVariable  

@PathVariable를 이용해 URI에 있는 값을 가져와서 사용할 수 있다.  

쿼리 스트링 방식의 URI가 아닌 RESTful하게 설계된 URI에 있는 값을 가져올 수 있다.  

```text
/board/view/2
```

사용자가 `/board/view/2` 라고 요청을 보냈을 때 `2`라는 식별자 값을 가져와 서버에서 처리한 후 2번 째 게시글에 해당하는 내용을 클라이언트에게 보여 줄 수 있다.   

스프링을 사용해 컨트롤러에서는 다음와 같이 사용할 수 있다.  

```java
@GetMapping(path = "/board/view/{id}")
public Board boardView(@PathVariable("id") Long boardId) {
    ...
}
```

경로 변수명과 메소드의 파라미터 이름이 같으면 괄호를 사용하지 않아도 된다. 다를 경우 괄호안에 경로 변수명을 지정해주면 된다.  

## 다중 값 받기

```java
@GetMappting(path = "/user/{userId}/orders/{orderId}")
public Order orderInfo(@PathVariable Long userId, @PathVariable Long orderId) {
    ...
}
```

Map을 사용해서 받을 수도 있다.  

```java
@GetMappting(path = "/user/{userId}/orders/{orderId}")
public Order orderInfo(@PathVariable Map<String, Long> map) {
    map.get(userId);
    map.get(orderId);
    ...
}
```

## 선택적 경로 변수  

```java
@GetMapping(path = {"/api/boards", "/api/boards/{pagenum}"})
```

게시판 페이징에서 첫 페이지는 깔끔하게 숫자 표시 안하고 `/api/boards`라는 경로로 끝내고 싶고, 두 번째 페이지부터 `/api/boards/2`, `/api/boards/3` 요렇게 표시하고 싶다는 요구사항이 있다고 할 때  

하지만 `api/boards`라고 요청을 보내면 처리하지 않는다. 그럼 분리해서 핸들러 메소드를 만들면 되지 않을까 생각할 수 있다.  

```java
@GetMapping(path = "/api/boards")
public BoardPaging paging1() {
    ...
}

@GetMapping(path = "/api/boards/{pagenum}")
public BoardPaging paging2(@PathVariable("pagenum") Long pageNum) {
    ...
}
```

똑같은 기능을 하는 핸들러 메소드를 추가해 처리하는 것도 하나의 방법일 수 있지만 좋은 방법은 아닌 것 같다.  

@PathVariable은 @RequestParam 처럼 required 속성의 기본 값이 true이기 때문에 기본적으로 값이 들어와야 한다.  

required의 속성을 false로 만들어 해결할 수 있지만 아직 문제가 있다.  

```java
@GetMapping(path = {"/api/boards", "/api/boards/{pagenum}"})
public BoardPaging paging(@PathVariable(required = false) Long pagenum) {
    ...
}
```

만약 사용자가 `/api/boards`라고 요청을 보내면 pagenum에는 null이 담기기 때문에 Optional을 이용해 안전하게 null을 처리하는 것이 좋다.  

```java
@GetMapping(path = {"/api/boards", "/api/boards/{pagenum}"})
public String paging(@PathVariable(name = "pagenum", required = false) Optional<Long> pagenum) {
    Long pNum = pagenum.orElse(0L);
    ...
}
```

Optional인 상태에서 /api/boards 로 요청을 보내면 pagenum에는 Optional.emtpy()가 담긴다.