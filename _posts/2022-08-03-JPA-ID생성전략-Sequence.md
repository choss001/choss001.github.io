# JPA 아이디 생성 전략(Sequence)

오늘은 JPA ID 생성 전략중 공식 Hibernate Docuemnt에서 추천하는 Sequence 전략에 대해 말해보려고 합니다.  

<br />
<br />

### JPA entity identifier annotations

우리가 식별자 생성 과정에서 조작할 수 있는 Enum Values와 annotations JPA 명세서는 다음과 같습니다.
<p align="center">
  <img src="/images/identifier/SequenceGenerator.png" alt="book" width="1200"/>
</p>  

@Id 어노테이션은 엔티티를 위해서 필수고 무조건 unique constraint 테이블 컬럼에 매핑되어야됩니다.  
대부분의 경우에는 PK컬럼에 매핑됩니다.

@GeneratedValue 어노테이션을 지정하지 않는다면 엔티티의 식별자는 필수적으로 수동으로 할당 되어야됩니다.  
만약 엔티티가 third-party에 의해서 natural identifier가 할당된다면 @GeneratedValue 어노테이션을 지정할 필요가 없습니다.  

관계형 디비에서는 일반적으로 2개의 간단한 식별자를 지정할 수 있는데요
  - Natural Keys, 이건 외부 시스템에의 의해서 유일함이 보장됩니다.(주민등록번호, 전화번호 등등)
  - Surrogate Keys, IDENTITY, SEQUENCE와 같이 데이터베이스에 의해서 관리되는 키입니다.(Auto-increment, seqeunce 등등)

저희가 지금 주제로 하고 있는 SEQUENCE는 Surrogate Keys에 해당됩니다.


GenerationType Enum은 4개의 엔티티 식별자 전략이 있습니다.

  - IDENTITY 전략은 MySQL의 AUTO_INCREMENT처럼 테이블 식별자 컬럼을 사용합니다..  
  만약 관계형 데이터베이스가 SEQUENCE를 지원한다면 JPA와 하이버네이트를 위해서 SEQUENCE를 쓰는 것이 추천됩니다  
  왜냐하면 하이버네이스는 엔티티가 IDENTITY 전략을 취하고 있다면 JDBC 배칭을 사용하지 못하기 때문입니다.  
  
  - SEQUENCE 전략은 식별자 값을 생성하는데 있어서 데이터베이스 시퀀스 객체를 사용합니다.  
  이 전략은 JPA와 Hibernate를 사용한다면 가장 좋은 식별자 생성 전략입니다.
  
  -  TABLE 전략은 데이터베이스 시퀀스 생성을 분리된 테이블로 모방합니다.  
  이 전략은 성능 문제가 있어서 추천되지 않습니다. 따라서 안쓰는게 좋습니다.
  
  -  AUTO 전략은 데이터 베이스따라서 위에 3개의 전략중 하나를 선택합니다.


spring cloud contract vs pact

<br />
<br />
<br />
<br />

출처 : [https://vladmihalcea.com/jpa-entity-identifier-sequence/](https://vladmihalcea.com/jpa-entity-identifier-sequence/)  
[https://vladmihalcea.com/hibernate-hidden-gem-the-pooled-lo-optimizer/](https://vladmihalcea.com/hibernate-hidden-gem-the-pooled-lo-optimizer/)  
[https://www.baeldung.com/hibernate-identifiers](https://www.baeldung.com/hibernate-identifiers)
[https://discourse.hibernate.org/t/generated-value-strategy-auto/6481](https://discourse.hibernate.org/t/generated-value-strategy-auto/6481)  
[https://www.baeldung.com/hi-lo-algorithm-hibernate](https://www.baeldung.com/hi-lo-algorithm-hibernate)  
