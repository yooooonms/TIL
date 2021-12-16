# ⚙️ IoC와 DI에 대해서

## 🤔 제어의 역전(IoC, Inversion of Control)이란?

일반적으로 프로그램을 작성할 때는 개발자가 직접 프로그램의 흐름을 제어할 수 있지만 스프링 같은 프레임워크를 사용하면 개발자는 프레임워크를 실행하는 작업만 수행할 뿐 그 이후 개발자가 작성한 코드를 호출하지는 않는다.

이때 내가 작성한 코드를 생성, 초기화, 실행, 소멸을 처리해주는 것이 프레임워크의 컨테이너이다. 스프링의 경우 클라이언트가 요청을 보내면 요청에 맞게 개발자가 작성한 코드가 알아서 동작하고 요청에 적절한 응답을 다시 클라이언트에게 보내는 것을 알아서 해준다.

```java
@SpringBootApplication
public class FooApplication() {
  public static void main(String[] args) {
    SpringApplication.run(FooApplication.class, args);
  }
}
```

개발자는 단지 프레임워크를 실행만 할 뿐 프레임워크가 동작하고 나서는 관여하지 않는다. 클라이언트에서 요청이 들어왔을 때 어떤 객체를 조작할 것인지? 객체 간의 관계는 어떻게 처리할 것인지 등의 복잡한 관계를 대신 처리해주는 것이 컨테이너이다.

개발자는 적절한 설정 파일을 작성하고 클라이언트의 요청을 처리할 수 있는 비즈니스 로직만 작성하면 된다.

<center>
  <img src="/Spring/image/iocContainer.png" width="400" height="260">
</center>

즉 **제어의 역전**이란 제어권이 개발자가 아닌 컨테이너가 가진다는 것을 말한다. 객체의 생성, 소멸, 의존관계 주입 등 객체의 생명주기를 개발자가 아닌 컨테이너가 담당하는 것이다. 그래서 IoC 컨테이너라고 불린다.


#### 🌈 제어의 역전(IoC, Inversion of Control) 정리

- 객체의 제어권을 컨테이너가 가지고 있다.
- 개발자는 비즈니스 로직을 작성하는데 집중할 수 있다. IoC 컨테이너가 대신 처리해주기 때문
- IoC는 스프링만의 개념이 아니다.

## 🤔 의존성 주입(DI, Dependency Injection)이란?

의존성 주입에서 의존이란 **객체 간의 의존**을 말한다.

```java
public class MovieListener {
  private MovieFinder movieFinder;
}
```

한 클래스가 다른 클래스를 사용할 때 이를 **의존**한다고 표현한다. 위 예시의 경우 MovieListener가 MovieFinder를 의존한다고 할 수 있다.

또한 의존은 변경에 의해 영향을 받는 관계를 의미하기도 한다.

```java
public class MovieListener {
  private MovieFinder movieFinder;

  public Movie findMovie(String movieTitle) {
    return movieFinder.findMovieByTitle(movieTitle);
  }
}
```

MovieFinder의 findMovieByTitle() 메서드가 findByTitle()로 변경하면 이 메서드를 사용하는 MovieListener 클래스의 코드도 함께 변경된다. 이처럼 **변경에 따른 영향이 전파되는 관계**를 의존한다고 표현한다.

의존성 주입은 의존 객체를 직접 생성하지 않고 **외부에서 의존 객체를 넣어주는 것**이다.

일반적으로 프로그램을 작성할 때 프로그램의 주도권은 개발자에게 있기 때문에 의존 객체를 new를 통해 직접 생성할 수 있다.

```java
interface MovieFinder {...}
class ActionMovieFinder implements MovieFinder {...}

public class MovieListener {
  private MovieFinder movieFinder = new ActionMovieFinder();
}
```

하지만 new를 이용해 의존객체를 직접 생성하는 것은 두 클래스를 강하게 결합시킨다는 문제점이 있다.

두 클래스가 강하게 결합되어 있어서 만약에 ActionMovie가 아닌 다른 장르의 영화를 찾고자 한다면 MovieListener를 수정해줘야 한다. 변경의 유연성이 떨어진다. 이에 해결책으로 각각 다른 장르의 영화를 찾기위해 여러 개의 MovieListener 클래스를 만드는 것은 좋지 못한 해결책이다.

위와 같은 문제를 해결해 주는 것이 **의존성 주입**이다.

코드 내부에서 직접 new를 통해 의존객체를 생성하는 것이 아니라 외부에서 구체적인 타입을 결정하고 그것을 주입 해주겠다는 것이다.

```java
interface MovieFinder {...}
class ActionMovieFinder implements MovieFinder {...}
class SIFIMovieFinder implements MovieFinder {...}

public class MovieListener {
  private final MovieFinder movieFinder;
  public MovieListenter(MovieFinder movieFinder) {
    this.movieFinder = movieFinder;
  }
}
```

의존성 주입을 사용하면 MovieListener는 구현체가 무엇인지 관계없이 MovieFinder로 모든 요청을 처리할 수 있다.

두 클래스 간의 결합을 약하게해 변경에 유연함을 더해줄 뿐만아니라 다른 장르의 영화를 찾기위해 새로운 구현체를 추가해도 전혀 문제가 없다.

애플리케이션의 규모가 커지면서 클래스간의 의존성 역시 다양해지고 코드도 복잡해지는데 이를 일일히 관리하기에는 너무 부담이 크다. 그래서 Spring 같은 프레임워크를 사용하는 것이다. 프레임워크가 알아서 의존관계를 관리해주니 개발자는 비즈니스 로직에만 더욱 신경쓸 수 있다.

#### 🌈 의존성 주입(DI, Dependency Injection) 정리

- 인터페이스 기반 설계는 확장에 유리하다.
- 클래스간의 결합도를 낮춘다.
- 테스트 작성이 편리하다.

---

#### 📌 Reference

- **초보 웹 개발자를 위한 스프링5 프로그래밍 입문 | 최범균 저**
- **[인프런-스프링 핵심 원리 기본편 | 김영한](https://www.inflearn.com/course/스프링-핵심-원리-기본편)**
- **<https://mangkyu.tistory.com/150>**
- **<https://docs.spring.io/spring-framework/docs/current/reference/html/core.html>**