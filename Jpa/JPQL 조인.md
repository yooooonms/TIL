# JPQL JOIN  

SQL 조인과 기능은 같고 문법만 약간 다르다.  

## 내부 조인  

'INNER JOIN' 사용 INNER는 생략할 수 있다.  

```java
// 회원과 팀을 내부 조인해서 'teamA'에 소속된 회원을 조회하는 JPQL
String teamName = "teamA";
String query = "SELECT m FROM Member m INNER JOIN m.team t WHERE t.name = :teamName";

List<Member> members = entityManager.createQuery(query, Member.class)
    .setParameter("teamName", teamName)
    .getResultList();
```

JPQL 조인은 연관 필드를 사용한다. 위 예제에서 m.team이 연관 필드이다.  

연관 필드는 다른 엔티티와 연관관계를 가지기 위해 사용하는 필드를 말한다.  

- **From Member m:** 회원을 선택하고 'm'이라는 별칭을 주었다.
- **Member m JOIN m.team t:** 회원이 가지고 있는 연관 필드로 팀과 조인하고 't'라는 별칭을 주었다.

JPQL은 JOIN 명령어 다음에 **조인할 객체의 연관 필드**를 사용한다. 다음은 잘못된 예이다.  

`FROM Member m JOIN Team t`  

다음 쿼리는 'teamA'인 소속인 회원을 나이 내림차순으로 정렬하고 회원명과 팀명을 조회한다.  

```text
SELECT m.uername, t.name
FROM Member m 
JOIN m.team t
WHERE t.name = 'teamA'
ORDER BY m.age DESC
```

조인한 두 개의 엔티티를 조회하려면 다음과 같이 JPQL을 작성하면 된다.  

```text
SELECT m, t
FROM Member m
JOIN m.team t
```

서로 다른 타입의 두 엔티티를 조회했기 때문에 TypeQuery를 사용할 수 없기 때문에 다음처럼 조회해야 한다.  

```java
List<Object[]> result = entityManager.(query).getResultList();

for (Object row : result) {
    Member member = (Member) row[0];
    Team team = (Team) row[1];
}
```

## 외부 조인  

```text
SELECT m FROM Member m LEFT [OUTER] JOIN m.team t
```

외부 조인은 SQL의 외부 조인과 같다. OUTER는 생략 가능하므로 LEFT JOIN과 같이 사용할 수 있다.  

#### - 컬렉션 조인  

일대다 관계나 다대다 관계처럼 컬렉션을 사용하는 곳에 조인하는 것을 컬렉션 조인이라고 한다.  

- [회원 → 팀]으로의 조인은 다대일 조인이면서 **단일 값 연관 필드 (m.team)** 를 사용한다.
- [팀 → 회원]은 반대로 일대다 조인이면서 **컬렉션 값 연관 필드 (m.members)** 를 사용한다.

```text
SELECT t, m FROM Team t LEFT JOIN t.members m
```

t LEFT JOIN t.members는 팀과 팀이 보유한 회원목록을 **컬렉션 값 연관 필드**로 외부 조인했다.  

#### - 세타 조인  

WHERE 절을 사용해서 세타 조인을 할 수 있다. 참고로 **세타 조인은 내부 조인만 지원한다.**  

세타 조인을 사용하면 전혀 관계없는 엔티티도 조인할 수 있다.  

```text
// JQPL
SELECT count(m) FROM Member m, Team t WHERE m.username = t.name

// SQL
// 전혀 관련 없는 Member.username, Team.name을 조인한다.
SELECT COUNT(M.ID)
FROM MEMBER M CROSS JOIN TEAM T
WHERE M.USERNAME = T.NAME
```

## JOIN ON 절  

ON 절을 사용해 조인 대상을 필터링하고 조인할 수 있다.  

내부 조인의 ON 절은 WHERE 절을 사용할 때와 결과 같으므로 보통 ON 절은 외부 조인에서만 사용한다.  

```text
// 모든 회원을 조회하면서 회원과 연관된 팀도 함께 조회한다. 이때 팀은 이름이 teamA만 조회한다.

// JPQL
SELECT m, t FROM Member m
LEFT JOIN m.team t ON t.name = 'temaA'

// SQL
SELCT m.*, t.* FROM Member m
LEFT JOIN Team t ON m.TEAM_ID=t.id AND t.name='teamA'
```

AND t.name='teamA'로 조인 시점에 조인 대상을 필터링한다.  

---

#### 📌 Reference  

- 자바 ORM 표준 JPA 프로그래밍 | 김영한 저