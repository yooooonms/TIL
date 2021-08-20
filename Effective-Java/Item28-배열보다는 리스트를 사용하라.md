# 배열보다는 리스트를 사용하라  

#### - 불공변 (invariant)  

상속 관계에 상관없이 자기 타입만 허용하는 것  <T>

#### - 공변 (convariant)  

상위 클래스와 하위 클래스의 타입만 허용하는 것  <? extends T>

#### - 반공변 (contravariant)  

공변의 반대개념으로 하위 클래스와 상위 클래스의 타입만 허용하는 것  <? Super T>  

<br/>

제네릭은 불공변, 배열은 공변이다.  

```java
Object[] objects = new Long[10];
objects[0] = "foo"  // 런타임시 ArrayStoreException 발생

List<Object> objectList = new ArrayList<Long>();    // List는 제네릭 타입이기 때문에 불공변 컴파일 에러 발생
objectList.add("foo");
```

배열은 실체화(reify)가 된다. 실체화는 런타임에도 자신이 담기로 한 원소의 타입을 인지하고 확인하다.  

반면 제네릭은 타입 정보가 런타임시에 소거된다. 즉 실체화되지 않는다. 원소 타입을 컴파일타임에만 검사하며 런타임에는 알 수 없다.  

> 소거 (erasure): 제네릭이 지원되기 전의 레거시 코드와 제네릭 타입을 함께 사용할 수 있게 해주는 매커니즘

둘의 차이때문에 제네릭과 배열은 어울리지 않는다.  

- 배열: 공변, 실체화 (컴파일 타임 안전 X, 런타임 타임 안전 O)
- 제네릭: 불공변. 타입 소거 (컴파일 타임 안전 O, 런타임 타임 안전 X)

코드로 **new List<E>[], new List<String>[], new E[]** 처럼 작성하면 컴파일할 때 제네릭 배열 생성 오류가 난다.  

제네릭 배열을 만들지 못하게 막은 이유는 **타입 안전하지 않기 때문**이다.  

이를 허용한다면 컴파일러가 자동 생성한 형변환 코드에서 런타임에 ClassCastException이 발생할 수 있다. 런타임에 ClassCastException이 발생하는 일을 막아주겠다는 제네릭 타입 시스템의 취지에 맞지 않다.  

E, List<E>, List<String> 같은 타입을 **실체화 불가 타입(non-reifiable type)** 이라 한다.  

실체화 불가 타입은 실체화되지 않아서 런타임에는 컴파일타임보다 타입 정보를 적게 가지는 타입을 말한다.  

## 배열보다는 리스트를 사용하자  

```java
public class Chooser {
    private final Object[] choiceArray;

    public Chooser(Collections choices) {
        choiceArray = choices.toArray();
    }

    public Object choose() {
        Random rnd = ThreadLocalRandom.current();
        return choiceArray[rnd.nextInt(choiceArray.length)];
    }
}
```

choose() 메소드를 호출할 때마다 반환된 Object를 원하는 타입으로 형변환해야 한다.  

```java
public class Chooser<T> {
    private final T[] choiceArray;

    public Chooser(Collections<T> choices) {
        choiceArray = choices.toArray();
    }

    ...
}
```

위와 같이 제네릭으로 변경하면 unchecked cast 비검사 경고가 발생한다. 하지만 다음처럼 Object 배열을 T 배열로 형변환하면 괜찮다.  

```java
choiceArray = (T[]) choices.toArray();
```

비검사 경고는 T가 무슨 타입인지 알수 없으니 컴파일러는 형변환이 런타임에도 안전한지 보장할 수 없다는 메시지이다.  

**제네릭에서는 원소의 타입 정보가 소거되어 런타임에는 무슨 타입인지 알 수 없다.**  

비검사 경고를 제거하기위해 배열 대신 리스트를 사용하자  

```java
public class Chooser<T> {
    private final List<T> choiceList;

    public Chooser(Collections<T> choices) {
        choiceList = new ArrayList<T>(chices);
    }

    public Object choose() {
        Random rnd = ThreadLocalRandom.current();
        return choiceList.get(rnd.nextInt(choiceList.size()));
    }
}
```

코드양이 조금 늘었지만 런타임시 ClassCastException이 발생할 일이 없기 때문에 좋은 방법이다.  

---

#### 📌 Reference  

- Effective Java 3/E | Joshua Bloch