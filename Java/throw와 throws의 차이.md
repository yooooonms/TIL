# throw와 throws의 차이점  

#### - throw  

```java
try {
    throw new SomeException();
} catch (SomeException e) {
    e.printStackTrace();
} finally {
    ...
}
```

throw는 사용자가 강제로 예외를 발생시킬 때 사용된다.  

#### - throws  

호출하는 메소드에게 예외를 던질 때 사용된다. 호출하는 메소드는 예외를 처리해주어야 한다.  

```java
public void foo() throws SomeException() {
    try {
        ...
    } catch (SomeException e) {
        ...
    } finally {
        ...
    }
}
```