# 자바 가상 머신(JVM, Java Virtual Machine)  

자바 가상 머신은 자바 바이트 코드를 운영체제에 맞게 실행시키기 위한 가상의 기계이다.  

```text
자바 바이트 코드(Java bytecode)?

자바 가상 머신이 이해할 수 있는 언어로 변환된 자바 소스 코드
```

자바로 작성된 모든 프로그램은 자바 가상 머신에서만 실행될 수 있기 때문에 자바 프로그램을 실행하기 위해서는 자바 가상 머신이 설치되어 있어야 한다.  

> 자바 가상 머신은 운영체제에 종속적이기 때문에 운영체제에 맞는 자바 가상 머신을 설치해야 한다.

서로 다른 운영체제라도 자바 가상 머신만 설치되어 있다면 같은 자바 프로그램을 운영체제의 종류에 상관없이 실행할 수 있다.  

## 자바 가상 머신 구조  

<img src="/Java/image/jvm.png" width="800" height="600">  

### - Class Loader  

자바 가상 머신으로 클래스 파일(.class)을 로드하고 링크를 통해 배치하는 작업을 수행한다.  

`Runtime`시점에 클래스를 처음 참조할 때 해당 클래스의 인스턴스를 생성하면 클래스 로더를 통해 메모리에 로드한다.  

### - Excution Engine  

클래스 로더가 자바 가상 머신내 런타임 데이터 영역에 바이트 코드를 배치시키는데 이것을 실행 시키는 역할을 담당한다. 즉, 클래스를 실행시키는 역할이다.  

#### Interpreter  

자바 바이트 코드를 명령어 단위로 읽어 실행한다. 한 줄씩 실행하기 때문에 느리다는 점이 있다.  

#### JIT(Just-In-Time) compiler  

인터프리터 방식의 단점을 보완하기 위해 도입되었다.  

인터프리터 방식으로 실행하다가 해당 코드를 캐싱하여 같은 함수가 여러 번 호출될 때 바이트 코드 전체를 컴파일해 `native code`로 변경한다.  

그렇게 되면 반복된 바이트 코드는 `native code`로 바뀌어 있기 때문에 바로 실행할 수 있다.  

변환된 코드는 캐시에 저장되기 때문에 재사용시 다시 컴파일할 필요가 없다.  

### - Garbage Collector  

더 이상 사용하지 않는 메모리를 자동으로 제거해 준다. 개발자가 직접 메모리를 관리하지 않아도된다.  

메모리를 자동으로 제거하는 시간은 언제인지 알 수 없다. 참조가 없어지자마자 해제되는 것을 보장하지 않는다.  

### - Runtime Data Area  

#### Method Area  

메서드 영역은 모든 스레드가 공유하는 영역으로 JVM이 시작될 때 생성된다.  

JVM이 읽어 들인 각각의 클래스와 인터페이스에 대한 런타임 상수 풀, 필드와 메서드 코드, Static 변수, 메서드의 바이트코드 등을 보관한다.  

`Static Area or Class Area`라고도 불린다.  

#### Stack Area  

기본형 타입 변수의 값들이 저장되고 참조형 타입 변수는 참조 값만 저장되고 생성된 인스턴스는 `Heap Area`에 저장된다.  

별도의 스택 프레임에서 선언된 지역변수는 `Stack Area`에 저장된다.

메소드를 호출 할 때마다 개별적으로 스택 프레임이 생성되고 수행이 끝나면 프레임 별로 삭제한다.  

#### Heap Area  

생성된 인스턴스 필드들이 존재한다.  

`Stack Area`에서 참조 값을 이용해 참조형 변수가 `Heap Area`에 있는 인스턴스를 가리켜 제어할 수 있다.  

상속을 이용해 인스턴스를 생성했다면 상위 클래스의 인스턴스들도 함께 생성된다.  

Heap에 할당된 데이터는 GC의 대상으로 JVM 성능 등의 이슈에서 가장 많이 언급되는 공간이다.  

어떤 참조 변수도 이 영역에 있는 인스턴스를 참조하지 않으면 `Garbage Collector`에 의해 정리된다.  

## PC Register  

`Thread`가 생성될 때마다 생성되는 영역으로 현재 쓰레드가 실행되는 부분의 주소와 명령을 저장하고 있다.  

## Native Method Stack  

자바가 아닌 다른 언어로 작성된 `native code`를 실행시키기위한 메모리 영역이다.  

보통 `C`또는 `C++`의 코드를 수행하기 위한 영역이다.  

<br/>

쓰레드가 생성되었을 때 `Method Area`와 `Heap Area`는 모든 쓰레드가 공유하지만  

나머지 `Stack Area` `PC Register` `Native Method Stack`은 각각의 쓰레드마다 생성되고 공유되지 않는다.  

```text
조금씩 알아갈 때마다 내용을 추가/수정 해야겠다.
```

---

#### Reference  

- <https://velog.io/@litien/JVM-%EA%B5%AC%EC%A1%B0>
- <https://hanul-dev.netlify.app/java/%EC%9E%90%EB%B0%94%EA%B0%80%EB%A8%B8%EC%8B%A0(jvm)%EC%9D%B4%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80/>
- <https://ko.wikipedia.org/wiki/JIT_%EC%BB%B4%ED%8C%8C%EC%9D%BC>