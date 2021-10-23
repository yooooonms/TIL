# UserDetailsService  

Spring Security에서 UserDetailsService의 역할은 사용자의 정보를 검색하는 역할을 담당한다.  

Spring Security는 UserDetailsService를 구현한 구현 클래스가 일부 포함되어 있다.  

UserDetailsService는 인터페이스로 `loadUserByUsername()` 이라는 하나의 메소드만 가지고 있다.  

```java
public interface UserDetailsService {
    UserDetails loadUserByUsername(String username) throws UsernameNotFoundException;
}
```

loadUserByUsername() 메소드의 반환형은 UserDetails인데 UserDetails는 사용자 정보와 권한을 담고 있는 인터페이스이다.  

```java
public interface UserDetails extends Serializable {
    // 사용자의 권한 목록을 반환
    Collection(? extends GrandedAuthority) getAuthorities();

    // 사용자의 비밀번호를 반환
    String getPassword();

    // 사용자의 고유한 값을 반환 ex) 아이디, 이메일 처럼 중복이 없는 값
    String getUsername();

    // 계정의 만료 여부 default true (만료 안됨)
    boolean isAccountNotExpired();

    // 계정의 잠김 여부 default true (잠기지 않음)
    boolean isAccountNonLocked();

    // 비밀번호 만료 여부 defatult true (만료 안됨)
    boolean isCredentialNonExpired();

    // 계정의 활성화 여부 default true (활성화 됨)
    boolean isEnabled();
}
```

주로 UserDetails를 구현한 `org.springframework.security.core.userdetails.User` 클래스를 사용하거나 직접 VO 클래스를 만들어 UserDetails 구현하는 식으로 사용한다.  

[User class 바로가기](https://github.com/spring-projects/spring-security/blob/main/core/src/main/java/org/springframework/security/core/userdetails/User.java)  

## CustomUserDetailsService 구현하기  

주로 CustomUserDetailsService를 구현할 때는 XXXRepository를 주입받아 데이터베이스와 연결해 처리한다.  

```java
@Service
public class CustomUserDetailsService implements UserDetailsService {

    private final MemberRepository memberRepository;

    public CustomUserDetailsService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }

    @Override
    public UserDetails loadUserByUsername(String identifier) throws UsernameNotFoundException {
        Member member = memberRepository.findByIdentifier(identifier)
            .orElseThrow(() -> new UsernameNotFoundException("Could not found member"));

        // User는 UserDetails를 구현한 클래스로 생성자와 빌더를 제공한다.
        // VO에 UserDetails를 구현하는 방법도 있지만 User를 상속받는 방법도 있다.
        return User.builder()
            .username(member.getIdentifier())
            .password(member.getPassword())
            .roles("USER")
            .build();
    }

}
```

여기서 주의할 점은 데이터베이스를 통해 조회한 사용자의 비밀번호는 암호화되어 있어야 한다. 즉 사용자를 저장할 때 비밀번호를 암호화해서 데이터베이스에 저장해야 한다.  

사용자가 화면에서 아이디와 비밀번호를 입력하고 DaoAuthenticationProvider에게 처리를 위임한다.  

DaoAuthenticationProvider는 UserDetailsService에서 사용자 정보를 조회하고 조회한 사용자 정보를 통해 UserDetails를 반환하는 역할을 담당한다.  

DaoAuthenticationProvider 내부 코드에 PassowordEncoder의 matches() 메소드를 통해 사용자가 화면에서 입력한 비밀번호와 UserDetailsService에서 반환된 UserDetails의 비밀번호를 비교한다.  

```java
// DaoAuthenticationProvider에서 오버라이딩된 additionalAuthenticationChecks 메소드이다.
// additionalAuthenticationChecks는 사용자가 입력한 정보 (아이디, 비밀번호)와 userDetails의 정보를 비교해 인증하는 메소드이다.
@Override
@SuppressWarings("deprecation")
public void additionalAuthenticationChecks(UserDetails userDetails, UsernamePasswordAuthenticationToken authentication) throws AuthenticationException {
    // UserDetails는 UserDetailsService에서 데이터베이스 조회 후 반환 값이고
    // UsernamePasswordAuthenticationToken은 사용자가 로그인 폼에서 입력한 아이디와 비밀번호이다.
    if (authentication.getCredentials() == null) {
        this.logger.debug("Failed to authenticate since no credentials provided");
        throw new BadCredentialsException(this.messages.getMessage("AbstractUserDetailsAuthenticationProvider.badCredentials", "Bad credentials"));
    }
    String presentedPassword = authentication.getCredentials().toString();

    // 비밀번호 비교
    if (!this.passwordEncoder.mathces(presentedPassword, userDetails.getPassword())) {
        this.logger.debug("Failed to authenticate since password does not match stored value");
        throw new BadCredentialsException(this.messages.getMessage("AbstractUserDetailsAuthenticationProvider.badCredentials", "Bad credentials"));
    }
}
```

Spring Security의 비밀번호 암호화 방식은 단방향 암호화로 쉽게 복호화해 사용자의 패스워드를 볼 수 없다. 때문에 저장된 패스워드 문자열을 PasswordEncoder의 matches 메소드를 통해 비교할 수 있게 해놓았다.  

```java
// 암호화 되지 않은 비밀번호(rawPassword)와 암호화된 비밀번호(encodedPassword)가 일치하는지 비교한다.
boolean matches(CharSequence rawPassword, String encodedPassword);
```