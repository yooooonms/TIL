스프링에서 컨트톨러 클래스로 지정해주기 위한 어노테이션은 다음 두 가지가 있다.  

- @Controller
- @RestController

둘의 차이점은 HTTP Response Body가 생성되는 방식에 있다.  

@Controller는 View를 반환하는 것이고, @RestController는 json 같은 형식의 데이터를 Http Message Body에 반환한다.  

## @Controller  

@Controller는 전통적인 스프링 MVC의 컨트롤러로서 주로 View를 반환하기 위해 사용된다.   

View를 반환하기 위해 ViewResolver가 사용되며 ViewResolver 설정에 적절하게 View를 찾아 렌더링한다.  

#### - @Controller Data 반환  

View대신 Data를 반환해야 하는 경우도 있다.  

@Controller는 기본적으로 View를 반환하지만 @ResponseBody와 함께 사용해 데이터를 반환할 수 있다.  

이때 반환되는 데이터 형식은 json이나 xml 등 다양하게 개발자가 설정할 수 있다.  

#### - @Controller 예제코드  

```java
@Controller
public class FooController {
    @GetMapping(path = "/signup")
    public String signup() {
        return "signup";
    }

    @GetMapping(path = "/mypage")
    @ResponseBody
    public Member mypage() {
        ...
        return member;
    }
}
```

## @RestController  

@RestController는 @Controller에 @ResponseBody가 추가된 형태의 어노테이션이다.  

```text
@RestController = @Controller + @ResponseBody
```

@Controller에서 데이터를 반환하기 위해 메소드마다 @ResponseBody를 붙여줄 필요가 없이 @RestController에서 작성한 메소드는 전부 Json이나 xml 등으로 반환할 수 있다.  

#### - @RestController 예제코드  

```java
@RestController
public class FooRestController {
    @GetMappgin(path = "/mypage")
    public Member mypage() {
        ...
        return member;
    }
}
```