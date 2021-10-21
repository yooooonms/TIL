# Inner Class  

내부 클래스는 클래스 내에 선언된다는 점을 제외하고는 일반적인 클래스와 크게 다르지 않다.  

```java
class A { // outer class

    class B { // inner class

    }
}
```

이름에서 알 수 있듯이 내부 클래스는 클래스 내에 선언된 클래스이다.  

```text
내부 클래스의 장점
- 내부 클래스에서 외부 클래스의 멤버들을 쉽게 접근할 수 있다.
- 외부에는 불필요한 클래스를 감춤으로써 캡슐화를 할 수 있다.
- 가독성을 높이고 유지 보수하기 편한 좋은 코드를 작성할 수 있다.
```

내부 클래스를 사용하는 경우 외부 클래스를 제외 하고는 다른 클래스에서는 잘 사용하지 않는다.  

## Inner Class의 종류와 특징  

#### 1. 인스턴스 클래스 (instance class)  

외부 클래스의 멤버변수 선언 위치에 선언한다. 외부 클래스의 인스턴스 멤버처럼 다루어지며 주로 외부 클래스의 인스턴스 멤버들과 관련된 작업에 사용된다.  

#### 2. 스태틱 클래스 (static class)  

외부 클래스의 멤버변수 선언 위치에 선언한다. 외부 클래스의 static 멤버처럼 다루어지며 주로 외부 클래스의 static 멤버, 특히 static 메소드에서 사용될 목적으로 선언된다.  

#### 3. 지역 클래스 (local class)  

외부 클래스의 메소드나 초기화블럭 안에 선언하며, 선언된 영역 내부에서만 사용될 수 있다.  

#### 4. 익명 클래스 (anonymous class)  

클래스의 선언과 객체의 생성을 동시에 하는 이름없는 클래스이다.  

## 내부 클래스 사용  

```java
class Outer {
    class InstanceInner {...}
    static class StaticInner {...}

    void myMethod() {
        class LocalInner {...}
    }
}
```

변수가 선언된 위치에 따라 인스턴스 변수, 클래스 변수, 지역 변수로 구분되는 것처럼 내부 클래스도 선언된 위치에 따라 구분된다. 또한 각 내부 클래스의 선언위치에 따라 변수와 동일한 유효범위와 접근성을 가진다.  

위 예제 코드에서 InstanceInner와 StaticInner는 Outer의 멤버변수와 같은 위치에 선언되며 멤버변수와 같은 성질을 가진다.  

내부 클래스가 외부 클래스의 멤버로 간주되고 인스턴스 멤버와 스태틱 멤버 간의 규칙이 내부 클래스에도 똑같이 적용된다.  

내부 클래스도 클래스이기 때문에 abstract나 final 같은 제어자를 사용할 수 있으며 멤버변수들 처럼 private, protected 같이 접근제어자도 사용 할 수 있다.  

```java
class Outer {

    class InstanceInner {
        int iv = 100;
        static int cv = 100;            // 에러 발생 static 변수를 선언할 수 없다.
        final static int MAX = 100;     // final static은 상수이기 때문에 허용
    }

    static class StaticInner {
        int iv = 200;
        static int cv = 200;            // static 클래스만 static 멤버를 정의할 수 있다.
    }

    void myMethod() {
        class LocalInner {
            int iv = 300;
            static int cv = 300;        // 에러 발생 static 변수를 선언할 수 없다.
            final static int MAX = 300; // final static은 상수이기 때문에 허용
        }
    }

    public static void main(String[] args) {
        System.out.println(InstanceInner.MAX);  // 100
        System.out.println(StaticInner.cv);     // 200
    }
}
```

내부 클래스 중에서는 스태틱 클래스만 스태틱 멤버를 가질 수 있다. `final static`은 상수이기 때문에 모든 내부 클래스에서 사용할 수 있다.  

```java
public class Outer {

    class InstanceInner {}
    static class StaticInner {}

    // 인스턴스 멤버 간에는 서로 직접 접근이 가능하다.
    InstanceInner instanceInner = new InstanceInner();

    // 스태틱 멤버 간에는 서로 직접 접근이 가능하다.
    StaticInner staticInner = new StaticInner();

    static void staticMethod() {
        // Error! 스태틱 메소드는 인스턴스 멤버에 직접 접근할 수 없다.
        // InstanceInner instanceInner = new InstanceInner();

        StaticInner staticInner = new StaticInner();
        
        // 인스턴스 클래스는 외부 클래스를 먼저 생성해야만 접근할 수 있다.
        Outer outer = new Outer();
        InstanceInner instanceInner1 = outer.new InstanceInner();
    }

    void instanceMethod() {
        // 인스턴스 메소드는 인스턴스 멤버와 스태틱 멤버 모두 접근할 수 있다.
        InstanceInner instanceInner = new InstanceInner();
        StaticInner staticInner = new StaticInner();
        
        // Error! 메소드에 지역적으로 선언된 내부 클래스는 외부에서 접근할 수 없다.
        // LocalInner localInner = new LocalInner();
    }

    void myMethod() {
        class LocalInner {}
        LocalInner localInner = new LocalInner();
    }
}
```

```java
public class Outer {
    private int outerIv = 0;
    static int outerCv = 0;
    
    class InstanceInner {
        int iiv1 = outerIv; // 외부 클래스의 private 멤버도 접근할 수 있다.
        int iiv2 = outerCv;
    }
    
    static class StaticInner {
        // Error! 스태틱 클래스는 외부 클래스의 인스턴스 멤버에 접근할 수 없다.
        // int siv = outerIv;

        static int scv = outerCv;
    }
    
    void myMethod() {
        int lv = 0;
        final int LV = 0;
        
        class LocalInner {
            int liv1 = outerIv;
            int liv2 = outerCv;
            int liv3 = lv;
            int liv4 = LV;
        }
    }
    
}
```

InstanceInner는 Outer의 인스턴스 멤버이기 때문에 접근제어자가 private이더라도 outerIv, outerCv를 모두 사용할 수 있다.  

StaticClass는 Outer의 스태틱 멤버이기 때문에 outerIv와 InstanceInner를 사용할 수 없고, 스태틱 멤버인 outerCv만 사용할 수 있다.  

LocalInner는 Outer의 인스턴스 멤버와 스태틱 멤버를 모두 사용할 수 있으며 지역 클래스가 포함된 메소드(myMethod())에 정의된 지역변수도 사용할 수 있다.  

**단 final이 붙은 지역변수만 가능하다. 왜냐하면 메소드가 수행을 마치고 지역변수가 소멸되는 시점에도 지역 클래스의 인스턴스가 소멸된 지역변수를 참조하려는 경우가 발생할 수 있기 때문이다.**  

JDK 1.8부터 지역 클래스에서 접근하는 지역 변수 앞에 final을 생략할 수 있는데 생략할 경우 컴파일러가 자동으로 붙여준다.  

편의상 final을 생략할 수 있게 한 것이지 변수의 값이 바뀌는 문장이 있으면 컴파일 에러가 발생한다.  

```java
public static void main(String[] args) {
    // 인스턴스 클래스의 인스턴스를 생성하려면 외부 클래스의 인스턴스를 먼저 생성해야 한다.
    Outer outer = new Outer();
    Outer.InstanceInner li = outer.new InstanceInner();

    // 스태틱 내부 클래스는 외부 클래스를 먼저 생성할 필요가 없다.
    Outer.StaticInner si = new Outer.StaticInner();
}
```

## 익명 클래스 (Anonymous Class)  

다른 내부 클래스들과는 다르게 이름이 없다. 왜냐하면 선언과 객체의 생성을 동시에 하기 때문에 단 한번만 사용될 수 있고 오직 하나의 객체만을 생성할 수 있는 일화용 클래스이기 때문이다.  

이름이 없기 때문에 생성자도 가질 수 없고, 상위 클래스의 이름이나 구현하고자 하는 인터페이스의 이름을 사용해 정의하기 때문에 하나의 클래스로 상속받는 동시에 인터페이스를 구현하거나 둘 이상의 인터페이스를 구현할 수 없다.  

**오직 단 하나의 클래스를 상속받거나 단 하나의 인터페이스만을 구현할 수 있다.**  

---

### Reference  

- 자바의 정석 1 | 남궁성 저