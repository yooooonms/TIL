# @ModelAttribute  

`@ModelAttribute`는 사용위치에 따라 기능이 달라진다.  

메소드 명에 사용되는 경우와 파라미터 옆에 사용되는 경우로 나눌 수 있다.  

`@ModelAttribute`의 기능은 클라이언트가 `form`을 통해 전송하는 요청 파라미터들을 `setter`를 이용해 1대1로 객체에 바인딩 해준다.  

## 파라미터에 @ModelAttribute 사용하기  

```java
@PostMapping(path = "/signup")
public String signup(@ModelAttribute Signup signup) {
    ...
    return "redirect:/";
}
```

`@ModelAttribute`는 `model`에 객체를 담아주는 역할을 한다.  

즉 `signup` 객체를 `model`에 담는 코드인 `model.addAttribute("signup", signup)`를 작성하지 않아도 된다.  

내부적으로 `model.addAttribute("signup", signup)`로 담겨진다.  

만약 객체명과 참조변수 이름이 `SignupRequest request`라면 객체명을 기준으로 카멜케이스를 적용해 `model.addAttribute("signupRequest", request)`;로 담긴다.  

여기에 추가로 매핑시키는 파라미터의 타입이 객체의 타입과 일치하는지를 포함한 검증 작업을 진행한다.  

`setter` 메소드를 통해 값을 바인딩하려고 시도하다 실패해도 작업이 중단되지 않는 대신 `BindingException` 타입의 객체에 담겨 컨트롤러로 전달된다.  

이 경우 `Error`나 `BindingResult`를 통해 `error`를 확인할 수 있다.  

주의할 점은 `Error`나 `BindingResult`는 자신 바로 앞에 있는 파라미터에 대한 검증오류들만 전달해주기 때문에 `@ModelAttribute`가 붙은 파라미터 뒤에 선언되어야 한다.  

```java
@PostMapping(path = "/signup")
public String signup(@ModelAttribute Signup signup, BindingResult bindingResult) {
    ...
    return "redirect:/";
}
```


## 메소드에 @ModelAttribute 사용하기  

```java
@ModelAttribute
public void categories(Model model) {
    model.addAttribute("categories", Arrays.asList("book", "movie", "toy"));
}
```

메소드 위에 선언된 경우 해당 컨트롤러 클래스 내의 핸들러 메소드보다 먼저 실행된다.  

여러 핸들러에서 공통적으로 사용해야 하는 `model`이 있을 때 사용하면 좋다.  

---

#### 📌 Reference  

- 초보 웹 개발자를 위한 스프링 5 프로그래밍 입문 | 최범균 저
- <https://mangkyu.tistory.com/72>