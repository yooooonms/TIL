HTTP는 요청(request)과 응답(response)으로 이루어진다.  

클라이언트에서 서버로 통신하는 메시지를 Request Message라고 하며, 서버에서 클라이언트로 통신하는 메시지를 Response Message라고 한다.  

보통 웹에서 새로고침 없이 이루어지는 동작은 대부분 비동기 통신으로 이루어진다. 화면이 변할 때마다 화면 전체를 새로고침 필요 없이 필요한 부분만 데이터를 바꾸어주면 된다.  

이를 위해서 클라이언트는 서버로 요청 메시지의 본문에 데이터를 보내야하고 서버에서 클라이언트도 응답 메시지의 본문에 데이터를 보내야 한다.  

이 본문의 HTTP Message의 Body부분에 해당하며 즉, 요청 본문(RequestBody)과 응답 본문(ResponseBody)에 데이터를 담아서 보내야 한다.  

본문에 담기는 데이터 형식은 여러가지가 있지만 대부분 Json을 사용해 데이터를 주고 받는다.  

## @RequestBody & @ResponseBody

@RequestBody는 HTTP Request Message의 Body에 있는 내용을 객체로 변환하는 역할을 한다.  

반대로 @ResponseBody는 객체를 HTTP Response Message의 Body로 변환하는 역할을 한다.  

@RequestBody나 @ResponseBody를 통해 객체로 변환할 때 HttpMessageConverter를 사용한다.  

#### - HttpMessageConverter  

HttpMessageConverter는 요청 본문을 객체로 변환하거나 객체를 응답 본문으로 변경할 때 사용한다.  

사용하는 HttpMessageConverter는 여러가지가 있고 어떤 요청을 보냈는지에 따라 응답에 사용되는 HttpMessageConverter가 달라진다.  

[HttpMessageConverter 구현체 목록 바로가기](https://docs.spring.io/spring-framework/docs/current/reference/html/integration.html#rest-message-conversion)  

<br/>

요청이 json으로 온 경우 요청 헤더에 있는 컨텐츠 타입(Content-Type)을 알려줘야 한다. json이면 컨텐츠 타입은 `application/json` 이다.  

그리고 헤더의 컨텐츠 타입을 확인하고 json을 매핑할 수 있는 MappingJackson2HttpMessageConverter가 사용해 요청 본문에 담긴 데이터를 객체로 변환한다.  

```java
@RestController
@RequiredArgsConstructor
public class FooRestController {

    private final FooService fooService;

    @GetMapping(path = "/api/foo")
    public FooResponse foo(@RequestBody FooRequest dto) {
        FooResponse res = fooService.findFoo(dto);
        return res;
    }
}
```

> @Controller를 사용할 경우는 메소드에 @ResponseBody 어노테이션을 사용해야 한다.

예제 코드를 보면 @ReponseBody를 확인할 수 없는데 @RestController가 적용되어있어서 그렇다.  

@RestController는 @Controller + @ResponseBody이기 때문에 @RestController만 작성해도 해당 클래스의 모든 메소드는 @ResponseBody가 적용된다.

--- 

#### 📌 Reference  

- <https://docs.spring.io/spring-framework/docs/current/reference/html/integration.html#rest-message-conversion>
- <https://docs.spring.io/spring-framework/docs/current/reference/html/web.html>