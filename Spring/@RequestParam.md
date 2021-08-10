# @RequestParam  

`@RequestParam` 어노테이션을 이용해 HTTP 요청 파라미터를 1:1로 받을 수 있다.  

```html
<form action="/info" method="get">
    이름: <input type="text" name="name"> <br/>
    나이: <input type="text" name="age">
    <button type="submit">전송</button>
</form>
```

위와 같은 폼이있고 이름에 `yoon` 나이에 `123`을 입력해 전송 버튼을 클릭하면 아래와 같은 `URI`로 서버로 전송한다.  

`http://localhost:8080/info?name=yoon&age=123`  

그리고 컨트롤러에서는 아래와 같은 방식으로 HTTP 요청 파라미터를 받을 수 있다.  

```java
@GetMapping(path = "/info")
public String info(@RequestParam(value = "name") String name, @RequestParam(value = "age") int age) {
    ...
    return "redirect:/hello";
}
```

만약 HTTP 요청 파라미터의 key값 하고 컨트롤러의 파라미터의 이름이 같다면  `@ReuqestParam`의 `value` 속성을 생략해도 된다.  

`String, int, Integer`등 단순한 타입이면 `@RequestParam`을 생략할 수 있다. `@RequestParam`을 생략하면 내부적으로 `required=false`를 적용한다.  

> 어노테이션을 완전히 생략하는 경우는 좋지 않다.

단 **생략할 경우 HTML Form의 input name 속성의 값과 컨트롤러의 파라미터의 변수명이 동일해야 한다.**  

다를 경우 `null`값이 들어가는데 `String`같은 참조형은 `null`을 받을 수 있지만 기본형은 `null`값을 받을 수 없기 때문에 `500`에러가 발생한다.  

따라서 `null`값을 받을 수 있는 타입으로 변경하거나 `defaultValue`를 사용하는 것이 좋다.  

`@ReuqestParam`는 `required=true`이기 때문에 기본적으로 반드시 해당 파라미터가 전송되어야 한다.  

파라미터가 전송되지 않으면 `400 Error`가 발생한다. 반드시 필요한 파라미터가 아니라면 `required = false`로 설정하는 것이 좋다.  

주의할점은 클라이언트가 폼에 아무것도 입력하지 않고 전송을 하면 파라미터 이름은 있는데 값이 없게 되는데 이 경우 `빈문자("")`로 통과한다.  

해당 파라미터를 사용하지 않고 요청을 할 경우 기본 값이 필요하다면 `defaultValue` 속성을 사용해 기본 값을 지정할 수 있다.  

```java
@GetMapping(path = "/info")
public String info(@RequestParam(value = "name") String name, 
                   @RequestParam(value = "age", required = false, defaultValue = "10") int age) {
    ...
    return "redirect:/hello";
}
```

`defaultValue` 속성을 사용해 기본 값을 지정한 경우 클라이언트가 폼에 아무값도 입력하지 않고 전송해도 설정한 기본 값이 적용된다.  

Map이나 List를 사용해 값을 얻어올 수 있다.  

```java
// `http://localhost:8080/info?name=yoon&age=123`
@GetMapping(path = "/info")
public String info(@RequestParam Map<String, Object> allParams) {
    log.info("name = {}", allParams.get("name"));
    log.info("age  = {}", allParams.get("age"));
    return "redirect:/hello";
}
```

파라미터의 값이 1개가 확실한 경우 Map을 사용해도 좋다. 하지만 파라미터의 값이 여러 개라면 MultiValueMap 값을 받을 수 있다.  

#### 📌 Reference  

- <https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1/dashboard>