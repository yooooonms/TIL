# null이 아닌, 빈 컬렉션이나 배열을 반환하라  

```java
/*
* @return 매장 안의 모든 치즈 목록을 반환한다. 단, 재고가 하나도 없다면 null을 반환한다.
*/
public List<Cheese> getCheeses() {
    return cheeseInStock.isEmpty() ? null : new ArrayList<>(cheesInStock);
}
```

컬렉션이나 배열같은 컨테이너가 비었을 때 null을 반환하는 메소드를 사용할 때면 방어 코드를 작성해줘야 한다.  

```java
if (cheese != null && cheese.contains(Cheese.STILION)) {
    ...
}
```

null을 체크 하는 코드를 빠트린다면 오류가 생길 수 있기 때문에 명시해야 한다. 즉, 오류 처리코드가 늘어난다.  

근데 컨테이너를 새로 할당하는데 비용이 들기 때문에 그냥 null을 반환하는게 좋지 않을까? 전혀 아니다.  

1. 새롭게 할당하는 비용이 성능 저하의 주범이 아닌 이상 성능 차이는 신경 안써도 된다.
2. 빈 컬렉션과 배열은 굳이 새로 할당하지 않고도 반환할 수 있다.

물론 사용 패턴에 따라 빈 컬렉션 할당이 성능을 눈에 띄게 떨어 트릴 수 있다.   

하지만 **매번 똑같은 빈 불변 컬렉션을 반환** 하는 방법으로 해결할 수 있다.  

**ex) Collections.emptyList(), Collections.emptySet(), Collections.emptyMap()**  

```java
public List<Cheese> getCheese() {
    return cheeseInStock.isEmpty() ? Colletions.emptyList() : new ArrayList<>(cheesInStock);
}

```

배열도 null을 반환하는 대신 길이가 0인 배열을 반환하는 것이 좋다.  

```java
public Cheese[] getCheese() {
    return cheesesInStock.toArray(new Cheese[0]);
}
```

만약 위 방식이 성능을 저하시킬 것 같으면 길이가 0인 배열을 미리 선언해두고 매번 해당 배열을 반환하도록 하자 길이가 0인 배열은 모두 불변이기 때문이다.  

```java
private static final Cheese[] EMPTY_CHEESE_ARRAY = new Cheese[0];

public Cheese[] getCheese() {
    return cheesesInStock.toArray(EMPTY_CHEESE_ARRAY);
}
```

getCheese는 항상 EMPTY_CHEESE_ARRAY를 인수로 넘겨 toArray를 호출하기 때문에 cheesesInStock이 비었을 때면 언제나 EMPTY_CHEESE_ARRAY를 반환한다.  

결론은 **null을 반환하는 API는 사용하기 어렵고 오류 처리 코드도 늘어난다. 그렇다고 성능이 좋은 것도 아니다.**

---

#### 📌 Reference  

- Effective Java 3/E | Joshua Bloch