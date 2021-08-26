# toString을 항상 재정의하라  

toString() 메소드는 Object 클래스에 정의된 메소드로 별도로 오버라이딩하지 않는 이상 클래스에 적합한 문자열을 반환하는 경우는 거의 없다.  

toString()은 다음과 같이 **클래스_이름@16진수로_표시한_해시코드**를 반환한다.  

```java
// Object에 정의되어 있는 toString()
public String toString() {
    return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```  

toString() 메소드에 작성된 주석을 보면 다음과 같은 문장을 확인할 수 있다.  

```text
be a concise but informative representation that is easy for a person to read.  
It is recommended that all subclasses override this method.
```

번역해보니 **간결하면서 사람이 읽이 쉬운 형태의 유익한 정보를 반환해야 한다.** 그리고 **모든 하위 클래스는 이 메소드를 재정의하는 것이 좋다** 라고 나와있다.  

"Person@3f0ee7cb"형태보다는 "Person{name='yoon', age=13}"처럼 Person의 정보를 직접 알려주는 형태가 훨씬 유익한 정보를 알려준다.  

**toString()을 잘 구현한 클래스는 사용하기 즐겁고, 디버깅하기 쉽다.** 

toString() 메소드는 객체를 println, printf, 문자열 연결 연산자 '+', assert 구문에 넘길 때, 디버거가 객체를 출력할 때 자동으로 호출된다. 개발자가 직접 호출하지 않아도 다른 어딘가에서 사용된다.  

개발자가 작성한 객체를 참조하는 컴포넌트가 오류 메시지를 로깅할 대 자동으로 호출할 수 있는데 이때 toString() 메소드를 제대로 정의하지 않았다면 쓸모없는 메시지만 로그에 남는다.  

**toString() 메소드는 해당 객체가 가진 주요 정보를 반환하는 것이 모두 좋다.**  

이상적인 toString() 메소드는 스스로를 완벽히 설명하는 문자열이좋다.  

toString()을 구현할 때는 반환값의 포맷을 문서화할지 정해야 한다. 포맷을 명시하면 그 객체는 표준적, 명확, 사람이 읽을 수 있게 된다.  

다음은 Object에 정의되어 있는 toString() 메소드에 작성된 주석이다.  

```text
The toString method for class Object returns 
a string consisting of the name of the class of which the 
object is an instance, the at-sign character `@', and 
the unsigned hexadecimal representation of the 
hash code of the object. In other words, this method returns a string equal to the 
value of: 
getClass().getName() + '@' + Integer.toHexString(hashCode())

// papago 번역
클래스 Object에 대한 ToString 메서드는 개체가 인스턴스인 클래스의 이름, 
at-sign 문자 '@' 및 개체의 해시 코드에 대한 서명되지 않은 16진수 표현으로 구성된 문자열을 반환합니다. 
즉, 이 메서드는 다음 값과 같은 문자열을 반환합니다. getClass().getName() + '@' + Integer.toHexString(hashCode())
```

toString() 메소드가 어떤 포맷으로 반환되는지 설명되어있다.  

다만 포맷을 한번 명시하게 되면 해당 포맷에 얽매이게 된다. 포맷을 명시하지 않는 다면 향후 릴리스에서 정보를 더 넣거나 포맷을 개선할 수 있는 유연성을 얻게된다.  

**포맷을 명시하든 안하든 의도는 명확하게 밝혀야 한다.**  

```java
/**
*   약물에 관한 대략적인 설명을 반환한다.
*   다음은 이 설명의 일반적인 형태이나,
*   상세 형식은 정해지지 않았으며 향후 변경될 수 있다.
*   
*   "[약물 #9: 유형=사람, 냄새=테레빈유, 겉모슴=먹물]"
*/
@Override
public String toString() {
    ...
}
```

포맷이 바뀔 여지가 있음을 인지할 수 있다.  

정적 유틸리티 클래스는 toString()을 제공할 이유가 없고 대부분의 열거 타입역시 자바가 완벽한 toString을 제공하니 따로 재정의할 필요가 없다.  

하지만 하위 클래스들이 공유해야할 문자열 표현이 있는 추상 클래스라면 toString()을 재정의해줘야 한다.  

대다수의 컬렉션 구현체는 추상 컬렉션 클래스들의 toString() 메소드를 상속해 사용한다.  

## 🌈 결론  

모든 구체 클래스에서 Object의 toString() 메소드를 재정의하는 것이 좋다. (상위 클래스에서 이미 알맞게 재정의한 경우는 예외)  

toString() 메소드를 재정의한 클래스느 사용하기 편하고 클래스를 사용한 시스템을 디버깅하게 쉽게 해준다.  

toString() 메소드는 해당 객체에 관한 명확하고 유용한 정보를 읽기 좋은 형태로 반환하는 것이 좋다.  

---

#### 📌 Reference  

- Effective Java 3/E | Joshua Bloch