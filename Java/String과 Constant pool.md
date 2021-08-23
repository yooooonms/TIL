# String & Constant pool  

자바에서 String은 문자열을 나타내는 클래스이고 자주 사용되는 클래스 중 하나이다.  

String 인스턴스를 생성하는 방법은 크게 2가지가 있다.  

1. new 연산자 사용
2. 쌍따옴표 사용

## String의 비교  

```java
String strA = new String("Hello");
String strB = new String("Hello");

String strC = "Hello";
String strD = "Hello";

System.out.println(strA == strB);       // false
System.out.println(strA.equals(strB));  // true

System.out.println(strC == strD);       // true
System.out.println(strC.equals(strD));  // true
```

strA와 strB는 new를 사용해 인스턴스를 생성했기 때문에 Heap Area에는 각각 다른 인스턴스를 가리킨다.  

당연히 strA와 strB를 동일성(==) 비교하면 false가 나올 것이다. 그런데 왜 strC와 strD의 동일성 비교는 true가 나올까?  

예제 코드를 자바 메모리 구조로 살펴보면 다음과 같다.  

<img src="/Java/image/constantpool.png" width="430" height="350">  

strC와 strD는 String Constant pool에 있는 동일한 인스턴스를 가리키고 있다.  

## String Constant pool  

자바는 String 클래스를 특별취급한다.  

new 연산자가 아니라 쌍따옴표를 이용해 String 인스턴스를 생성할 경우 Heap Area가 아닌 String Constant pool에라는 장소에서 별도로 관리된다.  

> String Constant pool은 Heap Area 내부에 존재한다.  

`String strC = "Hello"`가 실행되면서 String Constant pool에 "Hello"를 생성한다.  

이후 `String strD = "Hello"`가 실행되면 JVM은 String Constant pool에 찾아가 이전에 같은 값을 가진 String 인스턴스가 있다면 해당 인스턴스의 참조 값을 반환한다.  

때문에 strC와 strD는 String Constant pool에 있는 동일한 인스턴스를 가리킨다.  

## intern()  

String에는 intern() 이라는 메소드가 있다.  

intern() 메소드는 해당 String과 값이 같은 String 인스턴스가 String Constant pool에 있으면 해당 참조값을 반환한다. 없다면 호출한 String 인스턴스를 String Constant pool에 추가하가고 해당 인스턴스의 참조값을 반환한다.  


```java
strA = strC.intern();
```

위 코드를 실행하면 strC가 가리키는 String Constant pool의 참조값을 반환한다.  

strA는 이제 strC가 가리키는 String Constant pool 참조값을 가리키기 때문에 strA == strC는 true가 된다.  