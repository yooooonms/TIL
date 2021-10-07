# PasswordEncoder  

Spring Security는 사용자의 비밀번호를 안전하게 저장할 수 있도록 비밀번호의 단방향 암호화를 지원하는 PasswordEncoder 인터페이스와 해당 구현체들을 제공한다.  

`PasswordEncoder` 인터페이스는 다음과 같이 간단하게 구성되어 있다.  

```java
public interface PasswordEncoder {

    // 비밀번호를 단방향 암호화한다.
    String encode(CharSequence rawPassword);

    // 암호화 되지 않은 비밀번호(rawPassword)와 암호화된 비밀번호(encodedPassword)가 일치하는지 비교한다.
    boolean matches(CharSequence rawPassword, String encodedPassword);

    // 기본적으로 false를 반환 Custom하게 구현할 경우 이를 기반으로 더 강력한 암호화 구현한다.
    default boolean upgradeEncoding(String encodedPassword) {
        return false;
    }

}
```

Spring Security 지원하는 PasswordEncoder 구현 클래스들은 다음과 같다.

<img src="/Spring-Security/image/passwordencoder.png" width="800" height="300">

위 사진처럼 다양한 구현체들이 있지만 Spring에서 권장하는 BcryptPasswordEncoder에 대해 알아보겠다.  

## BCryptPasswordEncoder  

bcrypt는 해시함수를 사용한 구현체로 암호 크래킹에 대한 저항성을 높이기 위해 의도적으로 느리게 설계되었다.  

BCryptPasswordEncoder의 속도는 강도(Strength)를 조정해 조절할 수 있다. 강도는 **4 ~ 31** 사이를 설정할 수 있으며 default 강도는 10이다.  

```java
// 사용 예시
BCryptPasswordEncoder encoder = new BCryptPasswordEncoder(16);
String result = encoder.encode("myPassword");
assertTrue(encoder.matches("myPassword", result));
```

생성자를 통해 강도를 설정할 수 있으며 별도로 지정하지 않으면 10의 강도를 사용한다.  

시스템에 따라 PasswordEncoder가 암호를 해독하는데 걸리는 시간은 달라질 수 있기 때문에 테스트하면서 속도를 조정해주는 것이 좋다.  

다음은 BCryptPasswordEncoder가 비밀번호를 암호화할 때 사용하는 encode() 메소드이다.  

```java
// BCryptPasswordEncoder의 encode 메소드
public String encode(CharSequence rawPassword) {
    String salt;
    if (this.strength > 0) {
        if (this.random != null) {
            salt = BCrypt.gensalt(this.strength, this.random);
        } else {
            salt = BCrypt.gensalt(this.strength);
        }
    } else {
        salt = BCrypt.gensalt();
    }
    // salt를 generate하고 rawPassword와 salt를 이용해 hashing을 한다
    return BCrypt.hashpw(rawPassword.toString(), salt);
}
```

encode() 메소드는 매번 임의의 `salt`를 생성해 비밀번호를 암호화하기 때문에 같은 비밀번호를 암호화해도 매번 다른 암호화된 비밀번호를 반환한다.  

```text
# salt
- 해시함수를 사용하기전에 원문에 임의의 문자열을 덧붙이는 작업이다.
```

```java
@Test
void password_encode_test() {
    String plain = "123456789";

    String encodedA = passwordEncoder.encode(plain);
    String encodedB = passwordEncoder.encode(plain);

    System.out.println("plain = " + plain);
    System.out.println("encodedA = " + encodedA);
    System.out.println("encodedB = " + encodedB);

    System.out.println("encodedA.equals(encodedB) = " + encodedA.equals(encodedB));
    System.out.println("passwordEncoder.matches(plain, encodedA) = " + passwordEncoder.matches(plain, encodedA));
    System.out.println("passwordEncoder.matches(plain, encodedB) = " + passwordEncoder.matches(plain, encodedB));
}
```

<img src="/Spring-Security/image/testresult.png" width="800" height="200">

같은 비밀번호일지라도 암호화 할 때마다 항상 다른 결과를 반환한다. 때문에 원본 평문과 암호화된 비밀번호를 비교할 때는 equals() 보다는 PasswordEncoder의 matches()를 사용하는 것이 좋다.  

BCryptPasswordEncoder를 통해 암호화된 문자열은 다음과 같은 형태에 속한다.  

```
$2a$[cost]$[22 character salt][31 charater hash]
```

- 2a: 해시 알고리즘 식별자
- 10: cost 2^10 = 1024
  - BCryptPasswordEncoder의 강도(Strength)이다. 별도로 지정하지 않으면 10으로 설정한다.
  - 가장 큰 값인 31이 좋아 보일 수 있지만 많은 사용자가 접속할 경우 부하가 발생할 수 있다.
- 16byte의 salt base64 encoded to 22 characters
- 24byte의 hash base64 encoded to 31 characters

---

#### 📌 Reference  

- <https://gompangs.tistory.com/entry/Spring-Password-Encoder>
- <https://velog.io/@corgi/Spring-Security-PasswordEncoder란-4kkyw8gi>
- <https://docs.spring.io/spring-security/site/docs/current/reference/html5/#authentication-password-storage-bcrypt>