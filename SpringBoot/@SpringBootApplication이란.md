# @SpringBootApplication

```java
// 스프링부트 프로젝트를 생성시 프로젝트 루트에 만들어지는 클래스
@SpringBootApplication
public class FooApplication {
  public static void main(String[] args) {
    SpringApplication.run(FooApplication.class, args);
  }
}
```

@SpringBootAplication 어노테이션은 자동 설정을 담당한다. 스프링 부트의 자동 설정, 스프링 빈을 읽고 생성한다. 프로젝트 실행 시 @SpringBootAplication 어노테이션이 있는 위치부터 설정을 읽어가기 때문에 이 어노테이션을 포함한 클래스는 프로젝트 루트에 위치해야 한다.

@SpringBootApplication 어노테이션은 여러 개의 다른 어노테이션을 포함하고 있다.

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
  @Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
  ...
}
```

자동 설정과 관련해 아래 3가지 어노테이션을 포함하고 있다.

- @SpringBootConfiguration
- @EnableAutoConfiguration
- @ComponentScan

### @EnableAutoConfiguration

<img src="/SpringBoot/image/auto-config.png" width="500" height="250">

스프링부트의 의존성중 하나인 org.springframework.boot:spring-boot-autoconfigure의 META-INF 폴더 안에 **spring.factories** 폴더에는 많은 수의 클래스가 기본 자동 설정 대상으로 작성되어 있다.

```properties
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\
...
```

해당 목록에 포함되어 있는 클래스는 @Configuration 어노테이션이 작성되어 있지 않더라도 스프링 빈으로 등록된다.

개발자가 직접  **spring.factories**에 클래스를 추가하면 해당 클래스도 자동 설정 대상에 포함된다. 하지만 모든 클래스가 스프링 빈으로 등록되는 것이 아닌 필요한 클래스들만 스프링 빈으로 등록한다.

### @ComponentScan

@ComponentScan은 다음 어노테이션이 포함된 클래스를 스프링 빈으로 등록시킨다.

- @Component
- @Repository
- @Service
- @Controller or @RestController
- @Configuration

---

#### 참고

- **[스프링 부트 공식문서](https://docs.spring.io/spring-boot/docs/current/reference/html/using.html#using)**