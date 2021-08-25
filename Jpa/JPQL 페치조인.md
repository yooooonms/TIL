# JPQL 페치 조인  

페치 조인은 SQL에서 이야기하는 조인의 종류가 아닌 JPQL에서 성능 최적화를 위해 제공하는 기능이다.  

연관된 엔티티나 컬렉션을 한 번에 같이 조회하는 기능으로 JOIN FETCH 명령어로 사용할 수 있다.  

```text
페치 조인 ::= [LEFT [OUTER] | INNER] JOIN FETCH 경로
```

## 엔티티 페치 조인  

회원 엔티티를 조회하면서 연관된 팀 엔티티도 함께 조회  

```text
SELECT m FROM Member m JOIN FETCH m.team
```

JOIN FETCH를 사용하면 연관된 엔티티나 컬렉션을 함께 조회할 수 있다. 일반적인 JPQL과 다르게 **페치 조인은 별칭을 사용할 수 없다.**  

> 하이버네이트는 페치 조인에도 별칭을 허용한다.

위 JPQL을 실행하면 다음 SQL을 실행한다.  

```text
SELECT M.*, T.* FROM MEMBER M INNER JOIN TEAM T ON M.TEAM_ID=T.ID
```

<img src="/Jpa/image/fetch1.png" width="550" height="450">  

```java
// 페치 조인 사용
String jpql = "SELECT m FROM Member m JOIN FETCH m.team";

List<Member> members = entityManager.createQuery(jqpl, Member.class).getResultList();

for (Member member : members) {
    // 페치 조인으로 회원과 팀을 조회하기 때문에 지연 로딩이 발생하지 않는다.
    System.out.println(member.getUsername() + " " + member.getTeam().name());
}

// 출력결과
memberA teamA
memberB teamA
memberC teamB
```

회원과 팀을 지연 로딩으로 설정했다고 했을 때, **회원을 조회할 때 페치 조인을 사용해서 팀도 함께 조회했으므로 연관된 팀 엔티티는 프록시가 아닌 실제 엔티티이다.**  

따라서 연관된 팀을 사용해도 지연 로딩이 일어나지 않는다. 그리고 프록시가 아닌 실제 엔티티이므로 회원 엔티티가 영속성 컨텍스트에서 분리되어 준영속 상태가 되어도 연관된 팀을 조회할 수 있다.  

## 컬렉션 페치 조인  

```text
// JPQL
SELECT t FROM Team T JOIN FETCH t.members WHERE t.name = 'teamA'

// SQL
SELECT T.*, M.* 
FROM TEAM T
INNER JOIN MEMBER M ON T.ID=M.TEAM_ID
WHERE T.NAME = 'teamA'
```

<img src="/Jpa/image/fetch2.png" width="600" height="350">  

TEAM 테이블에서 teamA는 하나지만 MEMBER 테이블과 조인하면서 결과가 증가해 위와 같이 teamA가 2건 조회되었다.  

```java
String jpql = "SELECT t FROM Team t JOIN FETCH t.members WHERE t.name = 'teamA'";
List<Team> teams = entityManager.createQuery(jpql, Team.class).getResultList();

for (Team team : teams) {
    System.out.println(team.getName());
    for (Member member : team.getMembers()) {
        // 페치 조인으로 팀과 회원을 함께 조회해서 지연 로딩이 발생하지 않는다.
        System.out.println("-> " + member.getUsername());
    }
}

// 출력 결과
teamA
-> memberA
-> memberB
teamA
-> memberA
-> memberB
```

같은 'teamA'가 2건 조회되었다.  

> 일대다 조인은 결과가 증가할 수 있지만 일대일, 다대일 결과는 증가하지 않는다.

## 페치 조인과 DISTINCT  

SQL은 DISTINCT는 중복된 결과를 제거하는 명령이다.  

JPQL의 DISTINCT 명령어는 SQL에 DISTINCT를 추가하는 것은 물론이고 애플리케이션에서 한 번 더 중복을 제거한다.  

```text
SELECT DISTINCT t 
FROM Team t JOIN FETCH t.members
WHERE t.name = 'teamA'
```

각 로우에 데이터가 다르면 SQL DISTINCT는 효과가 없다.  

`SELECT DISTINCT t`의 의미는 **팀 엔티티의 중복을 제거하는 것이다.**  

```text
teamA
-> memberA
-> memberB
```

## 페치 조인과 일반 조인의 차이  

```text
SELECT t FROM Team t JOIN t.memberts m WHERE t.name = 'teamA'
```

JPQL은 결과를 반환할 때 연관관계까지 고려하지 않고, SELECT 절에 지정한 엔티티만 조회할 뿐이다.  

따라서 팀 엔티티만 조회하고 연관된 회원 컬렉션은 조회하지 않는다.  

만약 회원 컬렉션을 지연 로딩으로 설정하면 프록시나 아직 초기화하지 않은 컬렉션 래퍼를 반환한다.  

즉시 로딩으로 설정하면 회원 컬렉션을 즉시 로딩하기 위해 쿼리를 한 번 더 실행한다.  

```text
SELECT t FROM Team t JOIN FETCH t.members WHERE t.name = 'teamA'
```

페치 조인을 사용하면 연관된 엔티티도 함께 조회한다.  

## 페치 조인의 특징과 한계  

페치 조인을 사용하면 SQL 한 번으로 연관된 엔티티들을 함께 조회할 수 있어서 SQL 호출 횟수를 줄여 성능을 최적화 할 수 있다.  

페치 조인은 글로벌 로딩 전략보다 우선한다.  

`@OneToMany(fetch = FetchType.LAZY) 글로벌 로딩 전략`  

글로벌 로딩 전략을 지연 로딩으로 설정해도 JPQL에서 페치 조인을 사용하면 페치 조인을 적용해서 함께 조회한다.  

최적화를 위해 글로벌 로딩 전력을 즉시 로딩으로 설정하면 애플리케이션 전에에서 항상 즉시 로딩이 일어난다.  

빠를 수 있지만 사용하지 않는 엔티티를 자주 로딩하므로 성능에 악영향을 끼칠 수 있다.  

글로벌 로딩 전략은 지연 로딩을 사용하고 최적화가 필요하면 페치 조인을 적용하는 것이 효과적이다.  

페치 조인을 사용하면 연관된 엔티티를 쿼리 시점에 조회하므로 지연 로딩이 발생하지 않는다.  

따라서 준영속 상태에서도 객체 그래프 탐색을 할 수 있다.  

#### - 페치 조인 대상에는 별칭을 줄 수 없다.  

JPA 표준은 지원하지 않지만 하이버네이트를 포함한 몇몇 구현체들은 페치 조인에 별칭을 지원한다.  

**별칭을 잘못 사용하면 연관된 데이터 수가 달라져 데이터 무결성이 깨질 수 있다.**  

#### - 둘 이상의 컬렉션을 페치 할 수 없다.  

구현체에 따라 되기도 하지만 컬렉션 * 컬렉션의 카테시안 곱이 만들어 질 수 있기 때문에 주의해야 한다.  

#### - 컬렉션을 패치 조인하면 페이징 API를 사용할 수 없다.  

컬렉션이 아닌 단일 값 연관 필드들은 페치 조인을 사용해도 페이징 API를 사용할 수 있다.  

하이버네이트에서 컬렉션을 페치 조인하고 페이징 API를 사용하면 경고 로그를 남기면서 **메모리에서 페이징 처리를**한다.  

데이터가 적으면 괜찮지만 데이터가 많으면 성능 이슈와 메모리 초과 예외가 발생할 수 있어 위험하다.  

<br/>

페치 조인은 SQL 한 번으로 연관된 여러 엔티티를 조회할 수 있어 성능최적화에 상당히 유용하다.  

여러 테이블을 조회해서 엔티티가 가진 모양이 아닌 다른 결과를 내야한다면 페치 조인을 사용하는 것보다 여러 테이블에서 필요한 필드들만 조회해서 DTO로 반환하는 것이 더 효과적일 수 있다.  

---

#### 📌 Reference  

- 자바 ORM 표준 JPA 프로그래밍 | 김영한 저