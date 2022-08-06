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

<br />
<br />
<br />
<br />

출처 : [https://vladmihalcea.com/jpa-entity-identifier-sequence/](https://vladmihalcea.com/jpa-entity-identifier-sequence/)  
[https://vladmihalcea.com/hibernate-hidden-gem-the-pooled-lo-optimizer/](https://vladmihalcea.com/hibernate-hidden-gem-the-pooled-lo-optimizer/)  
[https://www.baeldung.com/hibernate-identifiers](https://www.baeldung.com/hibernate-identifiers)
[https://discourse.hibernate.org/t/generated-value-strategy-auto/6481](https://discourse.hibernate.org/t/generated-value-strategy-auto/6481)  
[https://www.baeldung.com/hi-lo-algorithm-hibernate](https://www.baeldung.com/hi-lo-algorithm-hibernate)  
