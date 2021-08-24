# JPQL (Java Persistence Query Language)  

**엔티티 객체를 조회하는 객체지향 쿼리**  

문법은 SQL과 비슷하고 ANSI 표준 SQL이 제공하는 기능을 유사하게 지원한다. SQL을 추상화해서 특정 데이터베이스에 의존하지 않는다.  

JPQL은 엔티티 직접 조회, 묵시적 조인, 다형성 지원으로 SQL보다 간결하다.  

```java
// JPQL을 사용해 회원이름이 kim인 엔티티를 전부 조회
String jpql = "SELECT m FROM Member AS m WHERE m.username = 'kim'";
List<Member> resultList = entityManager.createQuery(jpql, Member.class).getResultList();
```

JPQL에서 Member는 테이블이 아니라 **Member 엔티티**이고, m.username은 테이블 컬럼명이 아니라 **Member 엔티티의 필드명**이다.  

entityManager.createQuery() 메소드에 JPQL과 반환할 엔티티의 클래스 타입인 Member를 넘겨주고 getResultList() 메소드를 실행하면 JPA는 JPQL을 SQL로 변환해서 데이터베이스를 조회한다.  

#### - JPQL의 특징  

- 테이블을 대상으로 쿼리하는 것이 아닌 엔티티 객체를 대상으로 쿼리한다.
- SQL을 추상화해서 특정 데이터베이스 SQL에 의존하지 않는다.
- JPQL은 결국 SQL로 변환된다.

#### - 기본 문법과 쿼리 API  

```text
SELECT_문 :: = 
    SELECT_절
    FROM_절
    [WHERE_절]
    [GROUPBY_절]
    [HAVING_절]
    [ORDERBY_절]

UPDATE_문 :: = UPDATE_절 [WHERE_절]
DELETE_문 :: = DELETE_절 [WHERE_절]
```

## SELECT 문  

```java
SELECT m FROM Member AS m WHERE m.username = 'kim'
```

#### - 대소문자 구분  

엔티티와 속성은 대소문자를 구분한다.  

Member와 username은 대소문자를 구분하지만 SELECT, FROM, AS 같은 JPQL 키워드는 대소문자를 구분하지 않는다.  

#### - 엔티티 이름  

Member는 클래스 이름이 아니라 엔티티 이름이다.  

엔티티 이름은 @Entity(name = "XXX")로 지정할 수 있다. 엔티티 이름을 지정하지 않으면 클래스 이름을 기본값으로 사용한다.  

#### - 별칭은 필수  

Member AS m에서 Member에 m이라는 별칭을 주었다. JPQL은 별칭을 필수로 사용해야 한다.  

단 AS는 생략할 수 있다. Member m처럼 사용해도 된다.  

## TypeQuery, Query  

반환할 타입을 명확하게 지정할 수 있으면 TypeQuery 객체를 반환 타입을 명확하게 지정할 수 없으면 Query 객체를 사용하면 된다.  

```java
// TypeQuery 사용

TypedQuery<Member> query = entityManager.createQuery("SELECT m FROM Member m", Member.class);

List<Member> resultList = query.getResultList();
for (Member member : resultList) {
    ...
}
```

entityManager.createQuery()의 두 번째 파라미터에 반환할 타입을 지정하면 TypeQuery를 반환하고 지정하지 않으면 Query를 반환한다.  

```java
// Query 사용

Query query = entityManager.createQuery("SELECT m.username, m.age FROM Member m");

List resultList = query.getResultList();

for (Object o : resultList) {
    Object[] result = (Object[]) o; // 결과가 둘 이상이면 Object[] 반환
    System.out.println("username = " + result[0]);
    System.out.println("age = " + result[1]);
}
```

조회 대상이 String 타입인 회원 이름과, Integer 타입인 회원 나이이므로 조회 대상 타입이 명확하지 않다.  

SELECT 절에서 여러 엔티티나 컬럼을 선택할 때는 반환할 타입이 명확하지 않기 때문에 Query 객체를 사용해야 한다.  

Query 객체는 SELECT 절의 조회 대상이 하나면 Object를 반환하고 둘 이상이면 Object[]를 반환한다.  

**타입을 변환할 필요가 없는 TypeQuery를 사용하는 것이 더 편리하다.**  

#### - 결과 조회  

- query.getResultList(): 결과를 반환한다. 결과가 없으면 빈 컬렉션을 반환한다.
- query.getSingleResult(): 결과가 정확히 하나일 때 사용한다.
  - 결과가 없으면 javax.persistence.NoResultException 발생한다.
  - 결과가 1개보다 많으면 javax.persistence.NoUniqueResultException 발생한다.

## 파라미터 바인딩  

#### - 이름 기준 파라미터  

파라미터를 이름으로 구분하는 방법으로 앞에 **:**를 사용한다.  

```java
// 이름 기준 파라미터 사용

String usernameParam = "kim";

TypeQuery<Member> query = entityManger.createQuery("SELECT m FROM Member m WHERE m.username = :username", Member.class);

query.setParameter("username", usernameParam);
List<Member> resultList = query.getResultList();
```

:username이라는 이름을 기준으로 파라미터를 정의하고 query.setParameter()에서 username이라는 이름으로 파라미터를 바인딩한다.  

JPQL API는 대부분 메소드 체인 방식으로 설계되어 있기 때문에 다음과 같이 연속해서 작성할 수 있다.  

```java
List<Member> members = entityManager.createQuery("SELECT m FROM Member m WHERE m.username = :username", Member.class)
    .setParameter("username", "kim")
    .getResultList();
```

#### - 위치 기준 파라미터  

위치 기준 파라미터는 **?** 다음에 위치 값을 주면 된다.  

```java
// 위치 기준 파라미터 사용

List<Member> members = entityManger.createQuery("SELECT m FROM Member m WHERE m.username = ?1", Member.class)
    .setParameter(1, "kim")
    .getResultList();
```

위치 기준 파라미터 방식보다는 **이름 기준 파라미터 바인딩 방식을 사용하는 것이 좋다.**  

## 프로젝션  

SELECT 절에 조회할 대상을 지정하는 것을 프로젝션이라고 한다.  

```text
SELECT {프로젝션 대상} FROM
```

프로젝션 대상은 엔티티, 임베디드 타입, 스칼라 타입이 있다.  

스칼라 타입은 숫자, 문자 등 기본 데이터 타입을 말한다.  

#### - 엔티티 프로젝션  

```text
SELECT m FROM Member m          // 회원
SELECT m.team FROM Member m     // 팀
```

원하는 객체를 바로 조회하는 것이다. 이렇게 조회한 엔티티는 영속성 컨텍스트에서 관라된다.  

#### - 임베디드 타입 프로젝션  

JPQL에서 임베디드 타입은 엔티티와 거의 비슷하게 사용된다. 단, 임베디드 타입은 조회의 시작점이 될 수 없다는 제약이 있다.  

```java
// 잘못된 쿼리
String query = "SELECT a FROM Address a";
```

다음과 같이 엔티티를 통해서 임베디드 타입을 조회할 수 있다.  

```java
String query = "SELECT o.address FROM Order o";
List<Address> addresses = entityManager.createQuery(query, Address.class).getResultList();
```

**임베디드 타입은 엔티티 타입이 아닌 값 타입이기 때문에 직접 조회한 임베디드 타입은 영속성 컨텍스트에 관리되지 않는다.**  

#### - 스칼라 타입 프로젝션  

숫자, 문자, 날짜와 같은 기본 데이터 타입들을 스칼라 타입이라 한다.  

```java
// 전체 회원의 이름을 조회
List<String> usernames = entityManager.createQuery("SELECT username FROM Member m", String.class).getResultList();
```

중복 데이터를 제거하려면 DISTINCT를 사용한다.  

`SELECT DISTINCT username FROM Member m`  

다음과 같은 통계 쿼리도 주로 스칼라 타입으로 조회한다.  

```java
Double orderAmountAvg = entityManager.createQuery("SELECT AVG(o.orderAmount) FROM Order o", Double.class).getSingleResult();
```

#### - 여러 값 조회  

꼭 필요한 데이터들만 선택해서 조회할 때 유용하게 사용할 수 있다.  

프로젝션에서 여러 값을 선택하면 TypeQuery를 사용할 수 없고 Query를 사용해야 한다.  

```java
Query query = entityManager.createQuery("SELECT m.username, m.age FROM Member m");
List resultList = query.getResultList();

Iterator iterator = resultList.iterator();
while (iterator.hasNext()) {
    Object[] row = (Object[]) iterator.next();
    String username = (String) row[0];
    Integer age = (Integer) row[1];
}
```

제네릭에 Object[]를 사용하면 다음 처럼 코드를 더 간결하게 작성할 수 있다.  

```java
String query = "SELECT m.username, m.age FROM Member m";
List<Object[]> resultList = entityManager.createQuery(query).getReulstList();

for (Object[] row : resultList) {
    String username = (String) row[0];
    Integer age = (Integer) row[1];
}
```

스칼라 타입뿐만 아니라 엔티티 타입도 여러 값을 함꼐 조회할 수 있다.  

```java
String query = "SELECT o.member, o.product, o.orderAmount FROM Order o";
List<Object[]> resultList = entityManager.createQuery(query).getResultList();

for (Object[] row : resultList) {
    Member member = (Member) row[0];
    Product product = (Product) row[1];
    int orderAmount = (Integer) row[2];
}
```

조회한 엔티티는 영속성 컨텍스트에서 관리된다.  

#### - NEW 명령어  

DTO같은 의미있는 객체로 변환해서 사용하려면 NEW 명령어를 사용하면 된다.  

```java
public class UserDto {
    private String username;
    private Integer age;

    public UserDto(Stirng username, int age) {
        this.username = username;
        this.age = age;
    }
}

// NEW 명령어 사용
String query = "SELECT new jpa.example.UserDto(m.username, m.age) FROM Member m");
TypeQuery<UserDto> query = entityManager.createQuery(query, UserDto.class);
List<UserDto> resultList = query.getResultList();
```

SELECT 다음에 NEW 명령어를 사용해 반환받을 클래스를 지정할 수 있다 해당 클래스의 생성자에 JPQL 조회 결과를 넘겨줄 수 있다.  

NEW 명령어를 사용할 때는 다음 2가지를 주의해서 사용해야 한다.  

- 패키지 명을 포함한 전체 클래스 이름을 입력해야 한다.
- 순서와 타입이 일치하는 생성자가 필요하다.

## 페이징 API  

JPA는 페이징 API를 다음 두 API로 추상화 했다.  

- setFirstResult(int startPosition): 조회 시작 위치 (0부터 시작)
- setMaxResults(int maxResult): 조회할 데이터 수

```java
String jpql = "SELECT m FROM Member m ORDER BY m.username DESC";
TypeQuery<Member> query = entityManager.createQuery(query, Member.class);

query.setFirstReulst(10);
query.setMaxResults(20);
query.getResultList();
```

## 집합과 정렬  

```text
SELECT
    COUNT(m),   // 회원수
    SUM(m.age), // 나이 합
    AVG(m.age), // 평균 나이
    MAX(m.age), // 최대 나이
    MIN(m.age)  // 최소 나이
FROM Member m
```

#### - 집합 함수  

|함수|설명|
|:--|:--|
|COUNT|결과 수를 구한다. 반환 타입: Long|
|MAX, MIN|최대, 최소 값을 구한다. 문자, 숫자, 날짜 등에 사용한다.|
|AVG|평균값을 구한다. 숫자타입만 사용할 수 있다. 반환 타입: Double|
|SUM|합을 구한다. 숫자타입만 사용할 수 있다.<br/> 반환 타입: 정수합 Long, 소수합 Double, BigInteger합 BigInteger, BicDecimal합: BigDecimal|

#### - 집합 함수 사용 시 주의사항  

- NULL 값은 무시하므로 통계에 잡히지 않는다. (DISTINCT가 정의되어 있어도 무시된다)
- 값이 없는데 SUM, AVG, MAX, MIN 함수를 사용하면 NULL 값이 된다. (COUNT는 0이 된다)
- DISTINCT를 집함 함수 안에 사용해서 중복된 값을 제거하고 나서 집합을 구할 수 있다.
  - ex) `SELECT COUNT(DISTINCT m.age) FROM Member m`
- DISTINCT를 COUNT에서 사용할 때 임베디드 타입은 지원하지 않는다.

#### - GROUP BY, HAVING  

GROUP BY는 통계 데이터를 구할 때 특정 그룹끼리 묶어준다.  

```text
// 팀 이름을 기준으로 그룹별로 묶어서 통계 데이터를 구한다.
SELECT 
    t.name, COUNT(m.age), SUM(m.age). AVG(m.age), MAX(m.age), MIN(m.age)
FROM 
    Member m 
LEFT JOIN 
    m.team t 
GROUP BY 
    t.name
```

HAVING은 GROUP BY와 함께 사용한다. GROUP BY로 그룹화한 통계 데이터를 기준으로 필터링 한다.  

```text
// 그룹별 통계 데이터 중 평균 나이가 10살 이상인 그룹을 조회한다.
SELECT 
    t.name, COUNT(m.age), SUM(m.age). AVG(m.age), MAX(m.age), MIN(m.age)
FROM 
    Member m 
LEFT JOIN 
    m.team t 
GROUP BY 
    t.name
HAVING
    AVG(m.age) >= 10
```

문법은 다음과 같다.  

```text
GROUPBY_절 ::= GROUP BY {단일값 경로 | 별칭} + 
HAVING_절 ::= HAVING 조건식
```

#### - 정렬 ORDER BY  

결과를 정렬할 때 사용한다.  

```text
// 나이를 기준으로 내림차순으로 정렬하고 나이가 같으면 이름으로 오름차순으로 정렬한다.
SELECT m FROM Member m ORDER BY m.age DESE, m.username ASC
```

문법은 다음과 같다.  

```text
ORDERBY_절 ::= ORDER BY {상태필드 경로 | 결과변수 [ASC | DESC]} + 
```

- ASC: 오름차순 (기본값)
- DESC: 내림차순

상태필드는 `t.name` 같이 객체의 상태를 나타내는 필드를 말한다. 결과 변수는 SELECT 절에 나타나는 값을 말한다.  

```text
SELECT 
    t.name, COUNT(m.age) as cnt
FROM
    Member m LFET JOIN m.team t
GROUP BY
    t.name
ORDER BY
    cnt
```

cnt가 결과 변수이다.  

---

#### 📌 Reference  

- 자바 ORM 표준 JPA 프로그래밍 | 김영한 저