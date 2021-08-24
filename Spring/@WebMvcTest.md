# @WebMvcTest  

@WebMvcTest는 MVC를 테스트위한 어노테이션으로 컨트롤러를 테스트하는데 적합하다.  

클라이언트의 요청과 응답에 대한 테스트를 진행한다.  

@WebMvcTest 자체의 기능은 컨트롤러가 예상대로 동작하는지 테스트하는 것이다.

@Controller, @ControllerAdvise, @JsonComponent, Converter, GenericConverter, Filter, HandlerInterceptor, WebMvcConfigurer, HandlerMethodArgumentResolver 이 붙은 클래스만 스캔하도록 제한한다.  

자세한 정보는 다음 링크를 통해 확인할 수 있다.  

<https://docs.spring.io/spring-boot/docs/current/reference/html/test-auto-configuration.html#test-auto-configuration>  

> @Component, @Serivce, @Repository는 등록되지 않는다.

필요한 스프링 빈들만 등록하기 때문에 모든 스프링 빈을 등록하는 @SpringBootTest보다 가볍게 테스트를 진행할 수 있다.  

## @WebMvcTest 예시  

```java
@WebMvcTest(controllers = MemberController.class) // 테스트할 컨트롤러를 명시
public class MemberControllerTest {

    @Autowired
    private MockMvc mockMvc; // 클라이언트의 요청을 테스트할 컨트롤러로 전달하는 역할

    @MockBean
    public MemberService memberService; // 컨트롤러에서 사용하는 서비스가 등록되지 않기 때문에 @MockBean을 이용해 의존성 대체

    @Test
    void example_test() {
        mockMvc.perform(get("/signup"))
            .andExpect(status().isOk());
    }

}
```
WebApplication 관련된 스프링 Bean들만 등록하기 때문에 통합 테스트보다 빠르다.  

통합 테스트를 진행하기 어려운 테스트를 진행 가능하다.  

요청부터 응답까지 모든 테스트를 Mock 기반으로 테스트하기 때문에 실제 환경에서는 제대로 동작하지 않을 수 있다.  

#### 📌 Reference  

- <https://goddaehee.tistory.com/212?category=367461>