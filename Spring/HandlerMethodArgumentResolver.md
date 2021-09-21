# HandlerMethodArgumentResolver  

HandlerMethodArgumentResolver는 컨트롤러 클래스의 메소드에서 특정 조건에 맞는 파라미터가 있을 때 원하는 값을 바인딩해주는 인터페이스이다.  

@RequestBody를 사용해 요청 본문의 값을 받아올 때나 @PathVariable을 사용해 url의 경로 변수 값을 받아올 때 HandlerMethodArgumentResolver를 사용해 값을 받아온다.  

스프링은 기본적으로 여러가지 ArgumentResolver가 구현되어있다.  

PathVariableMethodArgumentResolver, RequestParamMethodArgumentResolver, RequestHeaderMapMethodArgumentResolver 등  

[Spring이 구현하고 있는 MethodArgumentResolver](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/method/support/HandlerMethodArgumentResolver.html)

HandlerMethodArgumentHandler 인터페이스는 다음과 같다.  

```java
public interface HandlerMethodArgumentHandler {

    boolean supportParameter(MethodParameter parameter);

    @Nullable
    Object resolveArgument(MethodParameter, @Nullable ModelAndViewController mavContainer, 
            NativeWebRequest webRequest, @Nullable WebDataBinderFactory binderFactory) throws Exception;

}
```

HandlerMethodArgumentHandler 인터페이스를 구현하는 클래는 위 두가지 메소드를 모두 구현해야 한다.  

#### - supportParameter  

현재 parameter를 resolver가 지원할지 true 또는 false로 반환한다.  

#### - resolveArgument  

실제 바인딩할 객체를 반환한다.  

네가지 파라미터를 제공하는데 의미는 다음과 같다.  

- **MethodParameter:** 확인할 메소드의 매개변수 해당 매개변수는 supportParameter에 의해 true가 반환되어야 한다.
- **ModelAndViewContainer:** 현재 요청(Request)에 대한 ModelAndViewContainer이다.
- **NativeWebRequest:** 현재 요청(Request)
- **WebDataBinderFactory:** WebDataBinder 인스턴스 생성을 위한 Factory


## 동작 방식  

<img src="/Spring/image/handlerMethodArgumentResovler.png" width="500" height="300">  

1. 클라이언트에서 요청을 보낸다.
2. 요청은 DispatcherServlet에서 처리된다.
3. 요청에 대한 HandlerMapping 처리
   1. RequestMapping에 대한 매칭
   2. Interceptor
   3. ArgumentResolver처리 (만약 Custom HandlerMethodArgumentResolver를 만들었다면 3-3에서 실행된다.)
4. 컨트롤러 메소드 실행


---

#### 📌 Reference  

- <https://webcoding-start.tistory.com/59>
- <https://blog.advenoh.pe.kr/spring/HandlerMethodArgumentResolver-%EC%9D%B4%EB%9E%80/>
- <https://jaehun2841.github.io/2018/08/10/2018-08-10-spring-argument-resolver/#spring-argument-resolver>
- <https://sanghye.tistory.com/41>
- 스프링 부트와 AWS로 혼자 구현하는 웹 서비스 | 이동욱 저