# @MappedSupperclass  

상위 클래스는 테이블과 매핑하지 않고 단순히 상위 클래스를 상속받는 하위 클래스에게 매핑 정보만 제공하고 싶을 때 사용한다.  

추상 클래스와 비슷한 역할을 한다. @Entity는 실제 테이블과 매핑되지만 @MappedSuperclass는 실제 테이블과는 매핑되지 않는다.  

단순히 매핑 정보를 상속할 목적으로만 사용되는 어노테이션이다.  

```java
@MappedSuperclass
public abstract class BaseEntity {

    @Id
    @GeneratedValue
    private Long id;

    private String name;

}

@Entity
public class Member extends BaseEntity {
    // id, name 상속
    ...
}

@Entity
public class Seller extends BaseEntity {
    // id, name 상속
    ...
}
```

객체들이 주로 사용하는 공통 매핑 정보를 정의했고 하위 엔티티들은 상속을 통해 BaseEntity의 매핑 정보를 물려받았다.  

BaseEntity는 테이블과 매핑할 필요가 없고 하위 엔티티에게 공통으로 사용되는 매핑 정보만 제공하면 되기 때문에 @MappedSuperclass를 사용했다.  

상위 클래스에게 상속받은 id, name 속성의 컬럼명을 변경하고 싶다면 다음처럼 하면된다.  

```java
@Entity
@AttributeOverrides({
    @AttributeOverride(name = "id", column = @Column(name = "MEMBER_ID")),
    @AttributeOverride(name = "name", column = @Column(name = "MEMBER_NAME")),
})
public class Member extends BaseEntity {
    ...
}
```

둘 이상을 재정의하려면 @AttributeOverrides를 사용하면 되고, 하나만 정의하려면 @AttributeOverride를 사용하면 된다.  

정리하면 @MappedSuperclass는 다음과 같은 특징을 가지고 있다.  

- 테이블과 매핑되지 않고 하위 클래스에 엔티티의 매핑 정보를 상속하기 위해 사용한다.
- @MappedSuperclass로 지정한 클래스는 엔티티가 아니기 때문에 entityManager.find()나 JPQL에서 사용할 수 없다.
- 해당 클래스를 직접 생성해서 사용할 일은 거의 없기때문에 추상 클래스로 만드는 것이 좋다.

@MappedSuperclass는 테이블과는 관계가 없고 단순히 엔티티가 공통으로 사용하는 매핑 정보를 모아주는 역할을 한다.  

@MappedSuperclass를 사용해 등록일자, 수정일자, 등록자, 수정자 같은 여러 엔티티에서 공통으로 사용하는 속성을 효과적으로 관리할 수 있다.  

> @Entity는 @Entity이거나 @MappedSuperclass로 지정한 클래스만 상속받을 수 있다.

---

#### 📌 Reference  

- 자바 ORM 표준 JPA 프로그래밍 | 김영한 저