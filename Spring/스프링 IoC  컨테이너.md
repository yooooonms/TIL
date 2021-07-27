# 스프링 IoC 컨테이너  

## 제어의 역전(IoC, Inversion of Control)  

제어의 역전이란 주도권이 개발자가아닌 프레임워크가 가진다는 것을 말한다.  

즉, 객체의 생성, 소멸, 의존 관계 주입 등 객체의 생명주기를 개발자가 아닌 프레임워크가 담당하는 것이다.  

> IoC는 스프링만의 개념이 아니다

## 스프링 IoC 컨테이너  

스프링 IoC 컨테이너는 객체의 생성, 소멸, 의존 관계 주입 등을 처리해주는 역할을 담당한다.  

> 요즘은 의존 관계 주입에 초점을 맞춰 DI 컨테이너라고도 부른다.

이렇게 스프링 IoC 컨테이너에의해 관리되는 객체들을 스프링에서는 `Bean`이라고 부른다.  

> Bean과 일반 자바 객체의 차이점은 없다. 단지 스프링 컨테이너에 의해서 관리되는 객체를 Bean이라고 하는 것이다.

개발자가 이 부분까지 신경쓰지 않아도 스프링 IoC 컨테이너가 관리해주기 때문에 개발자는 비즈니스 로직에 더 집중할 수 있다.  

스프링 컨테이너는 `BeanFactory`와 `ApplicationContext`로 나눌 수 있다.  

`BeanFactory`는 스프링 컨테이너의 최상위 인터페이스로 `Bean`을 관리하고 조회하는 역할을 담당한다.  

`ApplicationContext`는 `BeanFactory`를 상속받기 때문에 `BeanFactory`의 기능을 전부 이용할 수 있고 여기에 애플리케이션 개발에 필요한 부가기능을 제공한다.  

- 메시지 소스를 활용한 국제화 기능
- 환경변수
- 애플리케이션 이벤트
- 편리한 리소스 조회

일반적으로 스프링 컨테이너를 말할 때는 `ApplicationContext`를 말한다.  

---

#### 📌 Reference  

- <https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard>
- <https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#spring-core>