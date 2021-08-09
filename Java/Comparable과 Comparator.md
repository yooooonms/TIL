# Comparable & Comparator  

```text
Comparable: 기본 정렬기준을 구현하는데 사용한다.  
Comparator: 기본 정렬기준 외에 다른 기준으로 정렬하고자 할 때 사용한다.
```

`Comparable`을 구현하고 있는 클래스들은 같은 타입의 인스턴스끼리 비교할 수 있는 클래스들이다. 주로 `Integer`와 같은 `Wapper` 클래스 같은 것들이 있으며 기본적으로 오름차순으로 정렬되도록 구현되어있다.  

`Comparable`과 `Comparator`는 인터페이스로 컬렉션을 정렬하는데 필요한 메소드를 정의하고 있다.  

```java
public interface Comparable<T> {
    public int compareTo(Object o);
}

public interface Comparator<T> {
    int compare(Object o1, Object o2);

    boolean equals(Object obj);
}
```

`compareTo`와 `compare`는 선언형태와 이름이 다를 뿐이지 **두 객체를 비교**하는 같은 기능을 가지고 있다.  

다만 `compareTo`는 **자기 자신과 파라미터로 들어오는 객체를 비교**하는 것이고 `compare`는 **파라미터로 들어오는 두 객체를 비교**하는 것이다.  

두 메소드 모두 **비교하는 기능은 같지만 비교 대상은 다르다.**  

## Comparable 사용 예제  

`compareTo`는 자기 자신과 매개변수 객체를 비교한다. 학점을 기준으로 오름차순 정렬을 한다고 하면 아래처럼 구현할 수 있다.  

```java
@Override
public int compareTo(Student o) {
    if (this.score > o.score) {
        return 1;
    } else if (this.score == o.score) {
        return 0;
    } else {
        return -1;
    }
}
```

현재 객체보다 파라미터로 넘어온 객체가 크다면 음수를 반환한다. `this.score < o.score`  

현재 객체보다 파라미터로 넘어온 객체가 작다면 양수를 반환한다. `this.score > o.score`  

현재 객체보다 파라미터로 넘어온 객체가 같다면 0을 반환한다. `this.score == o.score`  

**반환 값이 음수 또는 0이면 교환하지 않고 자리가 유지되고 양수인 경우 두 객체의 자리가 바뀐다.**  

위 처럼 비교해서 반환해도 되지만 어차피 양수, 음수 또는 0을 반환하면 되는 것이기 때문에 비교 대상의 값 차이를 반환하는 것이 편한 방법이다.  

```java
@Override
public int compareTo(Student o) {
    return Double.compare(this.score, o.score);
}
```

자기 자신 객체의 점수에서 파라미터로 들어온 객체의 점수가 크다면 

`Student`의 `score`는 타입이 `double`이고 `compareTo`의 반환형은 `int`이기 때문에 타입을 맞춰서 반환해줘야 한다.  

```java
// 전체 코드
import java.util.Arrays;
import java.util.Comparator;

class Student implements Comparable<Student>{

    private int stCode;     // 학번
    private String name;    // 이름
    public double score;   // 학점

    public Student() {
    }

    public Student(int stCode, String name, double score) {
        this.stCode = stCode;
        this.name = name;
        this.score = score;
    }

    @Override
    public String toString() {
        return "Student{" +
                "stCode=" + stCode +
                ", name='" + name + '\'' +
                ", score=" + score +
                '}';
    }


    @Override
    public int compareTo(Student o) {
        return Double.compare(this.score, o.score);
    }
}

public class Main {
    public static void main(String[] args) {
        Student[] students = new Student[4];

        students[0] = new Student(1001, "test1", 4.2);
        students[1] = new Student(1002, "test2", 3.9);
        students[2] = new Student(1003, "test3", 4.5);
        students[3] = new Student(1004, "test4", 2.8);


        Arrays.sort(students);

        for (Student student : students) {
            System.out.println(student.toString());
        }

    }
}
```

## Comparator 사용 예제  

`compare`는 `compareTo`와는 다르게 파라미터터로 들어오는 두 객체를 비교하는 방식이다.  

`compareTo`는 선행 원소가 자기 자신이고 후행 원소가 매개 변수로 들어오는 객체 `o`이고, `compare`는 선행 원소가 `o1`이 되고 후행 원소가 `o2`가 된다.  

보통 `Comparator`를 사용할 때는 익명 클래스를 만들어서 사용한다.  

```java
Arrays.sort(students, new Comparator<Student>() {
    @Override
    public int compare(Student o1, Student o2) {
        return Double.compare(o1.score, o2.score);
    }
});
```

`compare`의 반환 값에 따른 정렬 방식은 `compareTo`와 동일하다.  

```java
// 전체 코드  
import java.util.Arrays;
import java.util.Comparator;

class Student {

    private int stCode;     // 학번
    private String name;    // 이름
    public double score;    // 학점

    public Student() {
    }

    public Student(int stCode, String name, double score) {
        this.stCode = stCode;
        this.name = name;
        this.score = score;
    }

    @Override
    public String toString() {
        return "Student{" +
                "stCode=" + stCode +
                ", name='" + name + '\'' +
                ", score=" + score +
                '}';
    }

}

public class Main {
    public static void main(String[] args) {
        Student[] students = new Student[4];

        students[0] = new Student(1001, "test1", 4.2);
        students[1] = new Student(1002, "test2", 3.9);
        students[2] = new Student(1003, "test3", 4.5);
        students[3] = new Student(1004, "test4", 2.8);


        Arrays.sort(students, new Comparator<Student>() {
            @Override
            public int compare(Student o1, Student o2) {
                return Double.compare(o1.score, o2.score);
            }
        });

        for (Student student : students) {
            System.out.println(student.toString());
        }
    }

}
```

---

#### 📌 Reference  

- 자바의 정석 2 | 남궁성 저