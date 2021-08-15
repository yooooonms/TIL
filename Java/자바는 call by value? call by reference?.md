# 자바는 Call by value? Call by reference?  

call by value냐 call by reference냐를 설명하기 위한 대표적인 예시는 두 수를 바꾸는 swap() 메소드가 있다.  

## Call by value 값에 의한 호출  

call by value는 가장 일반적인 함수 호출형태로 값을 복사하는 방식이다.  

```cpp
int swap(int a, int b) {
    int temp = a;
    a = b;
    b = temp;
}

int main() {
    int a = 1, b = 2;    
    printf("%d, %d", a, b);   // 1, 2
    swap(a, b);
    printf("%d, %d", a, b);   // 1, 2
    return 0;
}
```

main() 메소드의 변수 a, b와 add() 메소드의 a, b는 완전히 다른 별개의 변수이다.

따라서 swap 메소드에서 값을 바꾸더라도 원본 값은 변하지 않는다.  

이유는 함수를 호출할 때 main() 메소드의 a, b의 값이 복사되어 add() 메소드의 a, b로 전달되기 때문이다.  

## Call by reference 참조에 의한 호출  

```cpp
void swap(int* a, int* b) {
    int temp = a;
    a = b;
    b = temp;
}

int main() {
    int a = 1, b = 2;    
    printf("%d, %d", a, b);   // 1, 2
    swap(&a, &b);
    printf("%d, %d", a, b);   // 2, 1
    return 0;
}
```

swap() 메소드의 포인터 변수 a, b에는 main() 메소드의 a, b의 주소가 전달된다.  

call by value 처럼 값을 복사하는 것이 아니라 실제 주소를 전달 하기 때문에 main()의 a, b를 직접 참조할 수 있다.  

따라서 swap() 메소드에서 전달받은 값을 변경할 경우 원본도 같이 변경된다.  

## Java는 Call by value  

```java
public class Foo {
    public static void swap(int a, int b) {
        int temp = a;
        a = b;
        b = temp;
    }

    public static void main(String[] args) {
        int a = 1, b = 2;
        System.out.println(a + " " + b);    // 1 2
        swap(a, b);
        System.out.println(a + " " + b);    // 1 2
    }
}
```

자바 메모리 구조를 통해 보면 다음과 같다.  

<img src="/Java/image/callbyvalue1.png" width="400" height="450">  

swap 메소드가 호출되면서 call stack에 쌓이고 swap 메소드의 파라미터 a, b에는 값이 복사되어 전달된다.  

swap 메소드에서 a, b의 값을 서로 바꾼다음 swap 메소드가 종료되면서 swap에 있는 변수들도 함께 사라진다.  

즉 **원본이 전달된게 아니라 복사본이 전달되어 변경된 것이라 원본에는 아무런 영향을 미칠 수 없다.**  

```java
class Person {
    public String name;

    public Person(String name) {
        this.name = name;
    }
}

public class Main {
    public static void change(Person p) {
        p = new Person("korea");
    }

    public static void main(String[] args) {
        Person person = new Person("yoon");
        System.out.println(person.name);    // yoon
        change(person);
        System.out.println(person.name);    // yoon
    }
}
```

참조 값을 넘겼기 때문에 값이 변경되는 결과를 예상했지만 변경되지 않았다.  

자바 메모리 구조는 다음과 같다.  

<img src="/Java/image/callbyvalue2.png" width="400" height="450">  

change 메소드를 호출하면 call stack에 쌓이고 매개변수로 **person 참조값이 복사되어 전달**된다.  

이후 change 메소드의 `p = new Person("korea");`를 실행하면 새로운 Person 인스턴스가 Heap Area에 생성될 것이다.  

그리고 change 메소드가 역할을 다하고 종료되면 change frame은 소멸되기 때문에 main frame의 person에게는 아무런 영향을 주지 못한다.  

즉, **자바는 기본적으로 모든 전달 방식이 Call by value이다.**  

----

#### Reference  

- <http://wonwoo.ml/index.php/post/1679>