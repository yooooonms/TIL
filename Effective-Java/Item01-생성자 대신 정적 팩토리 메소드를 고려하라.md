# 생성자 대신 정적 팩토리 메소드를 고려하라  

```java
class Test {

    // public constructor
    public Test() {
    }

    public static void main(String[] args) {
        Test test = new Test();
    }

}
```

클래스의 인스터스를 얻는 가장 기본적인 방식은 `public` 생성자이다.  

클래스는 생성자와 별도로 **정적 팩토리 메소드(static factory method)** 를 제공할 수 있는데 개발자가 꼭 알아야할 기법이다.  

정적 팩토리 메소드는 클래스의 인스턴스를 반환하는 단순한 정적 메소드이다.  

```java
public static Boolean valueOf(boolean b) {
    return b ? Boolean.TRUE : Boolean.FALSE;
}
```

`Boolean`의 `valueOf` 메소드는 기본 타입인 `boolean`을 매개변수로 받아 `Boolean` 객체 참조로 변환해 주는 간단한 정적 팩토리 메소드이다.  

> 정적 팩토리 메소드는 디자인 패턴의 팩토리 메소드 패턴과는 아무런 관련이 없다.

그러면 생성자를 사용하지 않고 정적 팩토리 메소드로 제공하는게 좋을까?  

정적 팩토리 메소드는 장점과 단점을 가지고 있다.  

## 장점  

#### 1. 이름을 가질 수 있다.  

```java
// 객체의 특성을 설명할 수 없다.
Student student = new Student("yoon");
```

생성자에 넘기는 매개변수와 생성자 자체만으로는 반환될 객체의 특성을 설명할 수 없다.  

하지만 정적 팩토리 메소드는 이름을 잘 지으면 객체의 특성을 쉽게 표현할 수 있다.  

```java
class Student {

    private String name;

    public Student(String name) {
        this.name = name;
    }

    public static Student withName(String name) {
        return new Student(name);
    }

    public static void main(String[] args) {
        Student studentA = new Student("yoon");

        Student studentB = Student.withName("yoon");
    }

}
```

`studentA`보다는 `studentB`가 객체의 특성을 쉽게 표현하고 있다.  

생성자는 하나의 시그니처로 하나만 만들 수 있다.   

```java
public Student(String name) {
    this.name = name
}

// Error
public Student(String grade) {
    this.grade = grade;
}
```

물론 입력 매개변수들의 순서를 다르게해서 생성자를 추가하는 식으로 할 수 있지만 좋지 않은 방식이다.  

정적 팩토리 메소드는 이름을 가질 수 있기 때문에 위와 같은 제약이 사라진다.  

```java
public static Student withName(String name) {
    Student student = new Student();
    student.name = name;
    return student;
}


public static Student withGrade(String grade) {
    Student student = new Student();
    student.grade = grade;
    return student;
}
```

만약 한 클래스에 시그니처가 같은 생성자가 여러 개 필요할 것 같으면 생성자를 정적 팩토리 메소드로 바꾸가 각각의 차이를 알 수 있는 이름을 작성해주는 것이 좋다.  

#### 2. 호출될 때마다 인스턴스를 새로 생성하지 않아도 된다.  

`Boolean` 클래스의 `valueOf` 메소드는 새로운 인스턴스를 생성해서 반환하지 않는다.  

```java
public final class Boolean implements java.io.Serializable, Comparable<Boolean> {

    public static final Boolean TRUE = new Boolean(true);

    public static final Boolean FALSE = new Boolean(false);

    ...
}
```

`Boolean` 클래스는 `TRUE`와 `FALSE`를 상수로 미리 정의해 놓고 `valueOf`메소드에서 이를 반환한다.  

객체 생성 비용이 큰 객체가 자주 요청되는 상황이라면 위 방식을 사용해 성능을 상당히 끌어올릴 수 있다.  

이러한 장점으로 불변 클래스는 인스턴스를 미리 만들어 놓거나 새로 생성한 인스턴스를 캐싱하여 재활용하는 식으로 불필요한 객체 생성을 피할 수 있다.  

#### 3. 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.  

생성자는 반환값을 줄 수 없지만 정적 팩토리 메소드는 반환값을 유연하게 사용할 수 있어 클래스를 자유롭게 선택할 수 있는 유연성을 제공한다.  

```java
interface MovieFinder { }
class ActionMovieFinder implements MovieFinder { }
class HorrorMovieFinder implements MovieFinder { }
class SFMovieFinder implements MovieFinder { }

class MovieUtil {
    
    private MovieUtil() {
    }
    
    public static MovieFinder actionMovieFinder() {
        return new ActionMovieFinder();
    }

    public static MovieFinder horrorMovieFinder() {
        return new HorrorMovieFinder();
    }

    public static MovieFinder sfMovieFinder() {
        return new SFMovieFinder();
    }
    
}
```

#### 4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.  

반환 타입의 하위타입이기만 하면 어떤 클래스의 객체를 반환하든 상관없다.  

```java
class Weapon { }
class Gun extends Weapon { }
class Sword extends Weapon { }

class WeaponUtil {

    public static Weapon getWeapon(int damage) throws Exception {
        if (damage >= 0 && damage <= 49) {
            return new Swrod();
        } else if (damage >= 50 && damage <= 100) {
            return new Gun();
        } else {
            throw new Exception("no weapon");
        }
    }
}
```

공격력이 50미만이면 `Sword` 클래스를 반환하고 50이상이면 `Gun`클래스를 반환한다.  

만약 칼이 더이상 필요 없어서 삭제해도 문제 없다. 비슷하거나 더 강하고 성능을 개선한 무기를 다음에 추가할 수 있다.  

클라이언트는 정적 팩토리 메소드가 건네주는 것이 어느 클래스의 인스턴스인지 알 필요 없다.  

단지 `Wepon`의 하위 클래스이기만 하면 된다.  

#### 5. 정적 팩토리 메소드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.  

아직 이해가 가지 않는다. 추후에 이해가 되는 시점에 추가적인 내용을 보충 하도록 해야겠다.  

## 단점  

#### 1. 정적 팩토리 메소드만 제공하면 하위 클래스를 만들 수 없다.  

상속을 위해서는 public이나 protected 생성자가 필요하다.  

때문에 정적 팩터리 메소드만으로는 하위 클래스를 만들 수 없다.  

#### 2. 정적 팩토리 메소드는 프로그래머가 찾기 힘들다.  

생성자처럼 API 설명에 명확하게 드러나지 않아, 사용자가 정적 메소드를 찾아내야한다.  

## 정적 팩토리 메소드 명명 방식  

```
- from: 매개변수를 하나 받아서 해당 타입의 인스턴스를 반환하는 형변환 메소드
  ex) Date d = Date.from(instant);  

- of: 여러 매개변수를 받아 적합한 타입의 인스턴스를 반환하는 집계 메소드
  ex) Set<Rank> faceCards = EnumSet.of(JACK, QUEEN, KING);  

- valueOf: from과 of의 더 자세한 버전
  ex) BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE);  

- instance 혹은 getInstance: 매개변수를 받는다면 매게변수로 명시한 인스턴스를 반환하지만, 같은 인스턴스임을 보장하지는 않는다.
  ex) StackWalker luke = StackWalker,getInstance(options);  

- crete 혹은 newInstance: instance 혹은 getInstance와 같지만, 매번 새로운 인스턴스를 생성해 반환함을 보장한다.
  ex) Object newArray = Array.newInstance(classObject, arrayLen);  

- newType: newInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩토리 메소드를 정의할 때 쓴다. 'Type'은 팩토리 메소드가 반환할 객체의 타입이다.
  ex) BufferReader br = Files.newBufferedReader(path);  

- type: getType과 newType의 간결한 버전
  ex) List<Complaint> litany = Collections.list(legacyLitany);
```

---

#### 📌 Reference  

- Effective Java 3/E
- <https://velog.io/@litien/%EC%83%9D%EC%84%B1%EC%9E%90-%EB%8C%80%EC%8B%A0-%EC%A0%95%EC%A0%81-%ED%8C%A9%ED%86%A0%EB%A6%AC-%EB%A9%94%EC%84%9C%EB%93%9C%EB%A5%BC-%EA%B3%A0%EB%A0%A4%ED%95%98%EB%9D%BC>
- <https://devlog-wjdrbs96.tistory.com/256>