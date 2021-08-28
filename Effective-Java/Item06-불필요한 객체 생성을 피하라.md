# 불필요한 객체 생성을 피하라  

똑같은 기능의 객체를 매번 생성하기보다는 객체 하나를 재사용하는 편이 나을 때가 많다.  

> 불변 객체는 언제든 재사용할 수 있다.

```java
// 이런 코드는 좋지 않다!
String strA = new String("Hello");
```

예제코드는 실행될 때마다 Heap Area에 새로운 String 인스턴스를 생성한다.  

만약 해당 코드가 반복문이나 자주 호출되는 메소드안에 있다면 불필요한 String 인스턴스가 엄청 많이 만들어 질 수 있다.  

```java
// 개선
String strA = "Hello";
String strB = "Hello";

strA == strB;    // true!
```

이는 새로운 인스턴스를 매번 반드는 대신 하나의 String 인스턴스를 사용한다.  

같은 JVM에서 똑같은 문자열 리터럴을 사용하는 모든 코드는 같은 인스턴스를 재사용하는 것이 보장된다.  

`Constant Pool`에 있는지 검색 한 후에 String 인스턴스를 재사용한다.  

## 정적 팩토리 메소드를 제공하는 불변 클래스

생성자 대신 정적 팩토리 메소드를 제공하는 불변 클래스에서는 정적 팩토리 메소드를 사용해 불필요한 객체 생성을 피할 수 있다.  

때문에 생성자 대신 정적 팩토리 메소드를 사용하는 것이 좋다.  

```java
Boolean(...);           // deprecated
Boolean.valueOf(...);   // 권장하는 방법
```

생성자는 호출할 때마다 새로운 색체를 만들지만 팩토리 메소드는 전혀 그렇지 않다.  

불변 객체만이 아닌 가변 객체라 해도 사용 중에 변경되지 않을 것임을 안다면 재사용할 수 있다.  

## 생성 비용이 비싼 객체는 캐싱하여 재사용

생성 비용이 비싼 객체가 반복해서 필요하다면 캐싱하여 재사용하는 것이 좋은 방법이다.  

```java
static boolean isRomanNumber(String s) {
    return s.matches("정규식");
}
```

matches() 메소드는 성능이 중요한 상황해서 반복해 사용하기 적합하지 않다.  

왜? 정규 표현식용 Pattern 인스턴스를 한번 쓰고 버려서 GC에 대상이 되기 때문이다.  

성능 개선을 위해 정규 표션식을 표현하는 Pattern 인스턴스를 다음과 같이 초기화해 두고 재사용하는 것이 효율적인 방법  

```java
private static final Pattern ROMAN = "정규식";

static boolean isRomanNumber(String s) {
    return ROMAN.mathcer(s).matches();
}
```

## 불필요한 객체를 만들어내는 오토박싱  

오토박싱은 개발자가 기본 타입과 박싱된 기본 타입을 섞어 쓸 때 자동으로 변환해주는 기술  

```java
Integer num = 10;   // auto boxing
```

**오토 박싱은 기본 타입과 그에 대응하는 박싱된 기본 타입의 구분을 흐려주지만 완전히 없애주는 것은 아니다.**  

```java
private static long sum() {
    Long sum = 0L;
    for (long i = 0; i <= Integer.MAX_VALUE; i++) {
        sum = sum + i;  // 불필요한 Long 인스턴스가 생성된다.
    }
    return sum;
}
```

예제코드가 정확한 답을 내기는 하지만 엄청나게 느리다.  

`sum = sum + i`를 할 때 오토 박싱이 일어나기 때문이다. 불필요한 Long 인스턴스가 약 231개나 만들어진다.  

박싱된 기본 타입보다는 기본 타입을 사용하고, 의도치 않느 오토 박싱이 숨어들지 않게 조심하는 주의하자  

마지막으로 가벼운 객체를 다룰 때는 직접 만든 객체 풀보다 훨씬 빠르다. (요즘 JVM은 GC가 최적화가 잘되어있다)  

**객체 생성은 비싸니 피해야 하는 것으로 오해하지 말자**  

---

#### 📌 Reference  

- Effective Java 3/E | Joshua Bloch