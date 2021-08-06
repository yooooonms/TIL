# 생성자에 매개변수가 많다면 빌더를 고려하라  

정적 팩토리 메소드와 생성자는 선택적 매개변수가 많을 수록 적절히 대응하기 어렵다.  

예전에는 **점층적 생성자 패턴(telescopting constructor patter)** 을 사용했다.  

필수 매개변수를 받는 생성자, 필수 매개변수와 선택 매개변수를 하나씩 늘려가며 생성자를 만드는 패턴이다.  

이 패턴은 매개변수가 점점 늘어날 수록 코드를 작성하기 어렵고 읽기 매우 힘들다.  

```java
class Person {
    private String name;    // 필수
    private int age;        // 필수 
    private String gender;  // 선택
    private String tel;     // 선택
    private String hobby;   // 선택

    public Person(String name, int age, String gender, String tel, String hobby) {
        this.name = name;
        this.age = age;
        this.gender = gender;
        this.tel = tel;
        this.hobby = hobby;
    }

    public Person(String name, int age, String gender, String tel) {
        this(name, age, gender, tel, "");
    }

    public Person(String name, int age) {
        this(name, age, "", "", "");
    }

    ...
    
}
```

각 값의 의미가 무엇인지 알기 어렵고 매개 변수가 몇 개인지도 주의해서 세어 보아야 한다.  

```java
Person person = new Person("name", 25, "male", "000-0000-0000", "tv");
```

타입이 같은 매개변수가 연달아 늘어져 있을 때 위 예제에서 개발자의 실수로 `tv`랑 `male`을 바꿔서 쓴다면 찾기 어려운 버그로 이어진다.  

이는 오류가 아니기 때문에 컴파일러는 알아챌수 없고 결국 런타임시 이상한 동작을 하게 된다.  

두 번째 대안인 **자바빈즈 패턴(JavaBeans pattern)** 이 있다.  

```java
class Person {
    private String name;    // 필수
    private int age;        // 필수
    private String gender;  // 선택
    private String tel;     // 선택
    private String hobby;   // 선택
    
    public Person() {
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public void setGender(String gender) {
        this.gender = gender;
    }

    public void setTel(String tel) {
        this.tel = tel;
    }

    public void setHobby(String hobby) {
        this.hobby = hobby;
    }
    
}
```

매개변수가 없는 생성자로 객체를 만든 후, **수정자(setter)** 메소드들을 호출해 원하는 매개변수의 값을 설정하는 방식이다.  

```java
Person person = new Person();
person.setName("name");
person.setAge(0);
person.setGender("male");
person.setTel("000-0000-0000");
person.setHobby("tv");
```

점층적 생성자 패턴에 비해 코드가 길어지긴 했지만 인스턴스를 만들기 쉽고 더 읽기 쉬운 코드가 되었다.  

하지만 자바빈즈 패턴은 객체 하나를 만들려면 여러 개의 수정자 메소드를 호출해야 하고 객체가 완전히 생성되기 전까지는 일관성이 무너진 상태에 놓이게 된다.  

#### - 일관성(consistency)이 무너지다?  

```java
Person person = new Person();
person.setName("name");
person.setAge(0);
```

이렇게 객체를 생성하면 `gender, tel, hobby` 필드는 값이 세팅되지 않아 객체가 유효하지 않은 상태가 된다.  

만약 유효한 객체가 들어올 것이라고 예상한 메소드를 실행하게 되면 예외가 발생한다. 이런 경우를 일관성이 깨진다고 한다.  

## 해결책: 빌더 패턴(Builder patter)  

필요한 객체를 직접 만드는 대신 필수 매개변수만으로 생성자를 호출해 빌더 객체를 얻는다.  

```java
class Person {
    private final String name;    // 필수
    private final int age;        // 필수
    private final String gender;  // 선택
    private final String tel;     // 선택
    private final String hobby;   // 선택

    public static class Builder {
        // 필수 매개변수
        private final String name;
        private final int age;

        // 선택 매개변수 기본값으로 초기화
        private String gender = "";
        private String tel = "";
        private String hobby = "";

        public Builder(String name, int age) {
            this.name = name;
            this.age = age;
        }

        public Builder gender(String val) {
            gender = val;
            return this;
        }

        public Builder tel(String val) {
            tel = val;
            return this;
        }

        public Builder hobby(String val) {
            hobby = val;
            return this;
        }
        
        public Person build() {
            return new Person(this);
        }
    }

    private Person(Builder builder) {
        name = builder.name;
        age = builder.age;
        gender = builder.gender;
        tel = builder.tel;
        hobby = builder.hobby;
    }

}
```

`Person` 클래스는 불변으로 설계되었으며 모든 매개변수의 기본 값들을 한 곳으로 모아두었다.  

빌더의 `Setter` 메소드들은 빌더 자신을 반환하기 때문에 연쇄적으로 호출 할 수 있다.  

```java
Person person = new Person.Builder("name", 20)
        .gender("male")
        .tel("000-0000-0000")
        .hobby("game")
        .build();
```

메소드 호출이 연쇄적으로 호출하는 것을 `플루언트 메소드(fluent API)`또는 `메소드 연쇄(Method chaining)`이라 한다.  

빌더 패턴은 위에서 소개한 점층적 생성자 패턴과 자바빈즈 패턴에 비해 사용하기 쉽고 무엇보다 가독성이 뛰어나다는 점이 있다.  

빌더 패턴은 매개 변수가 4개 이상은 되어야 값어치를 하지만 `API`는 시간이 가면서 변하기 때문에 처음부터 빌더 패턴을 적용하는 것도 좋은 방법이다.  

생성자나 정적 팩토리 메소드가 처리해야할 매개변수가 많다면 빌더 패턴을 선택하는 것이 좋다.  

`Lombok`의 `@Builder`어노테이션과 결합하면 `Builder` 클래스를 개발자가 만들어 주지 않아도 되기 때문에 간결한 코드를 작성할 수 있다.  

---

#### 📌 Reference  

- Effective Java 3/E