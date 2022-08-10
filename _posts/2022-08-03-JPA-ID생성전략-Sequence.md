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

#### 자연키 vs 대체키
관계형 디비에서는 일반적으로 2개의 간단한 식별자를 지정할 수 있는데요
  - Natural Keys :  이건 외부 시스템에의 의해서 유일함이 보장됩니다.(주민등록번호, 전화번호 등등)
  - Surrogate Keys : IDENTITY, SEQUENCE와 같이 데이터베이스에 의해서 관리되는 키입니다.(Auto-increment, seqeunce 등등)

저희가 지금 주제로 하고 있는 SEQUENCE는 Surrogate Keys에 해당됩니다.


### GenerationType Enum 

  - IDENTITY 전략은 MySQL의 AUTO_INCREMENT처럼 테이블 식별자 컬럼을 사용합니다..  
  만약 관계형 데이터베이스가 SEQUENCE를 지원한다면 JPA와 하이버네이트를 위해서 SEQUENCE를 쓰는 것이 추천됩니다  
  왜냐하면 하이버네이스는 엔티티가 IDENTITY 전략을 취하고 있다면 JDBC 배칭을 사용하지 못하기 때문입니다.  
  
  - SEQUENCE 전략은 식별자 값을 생성하는데 있어서 데이터베이스 시퀀스 객체를 사용합니다.  
  이 전략은 JPA와 Hibernate를 사용한다면 가장 좋은 식별자 생성 전략입니다.
  
  -  TABLE 전략은 데이터베이스 시퀀스 생성을 분리된 테이블로 모방합니다.  
  이 전략은 성능 문제가 있어서 추천되지 않습니다. 따라서 안쓰는게 좋습니다.
  
  -  AUTO 전략은 데이터 베이스따라서 위에 3개의 전략중 하나를 선택합니다.


### Identity vs Sequence

3개의 전략중에서 성능 이슈가 있는 TABLE 전략을 제외하고 SEQUENCE 전략과 IDENTITY 전략 차이점은 다음과 같습니다.
 - Identity는 어플리케이션의 의해서 관리될수 없지만 Sequence는 어플리케이션 코드로 제어가 가능합니다.
 - 만약 컬럼이 Identity로 마크되어있다면 이 컬럼에 직접적으로 데이터를 넣을수 없지만 시퀀스 오브젝트는 테이블에 의존하지 않기 때문에 해당 컬럼에 어떤 데이터도 인서트 할 수 있습니다.
 - Identity는 데이터를 삽입하기전에 값을 가져올 수 없지만 Sequence는 데이터를 삽입하기전에 다음 벨류를 가져올 수 있습니다.
 - Sequence는 새롭게 씨를 뿌릴수도 있고 스텝 사이즈를 언제든지 변경할 수 있지만 Identity는 새롭게 다시 씨를 뿌릴수는 있지만 스텝 사이즈를 변경할 수 없습니다.
 - Sequence는 데이터베이스 전체 시퀀스 넘버를 생성할 수 있지만 Identity는 테이블에 묶여있습니다.

이러한 차이점은 다음과 같이 최적화에서 차이나게 됩니다.

아이디 생성 전략중 Identity 전략을 사용한 소스 먼저 보겠습니다.

#### Identity

```
@Entity
public class RestaurantOrderIdentity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column
    private String test;
}
```
엔티티는 위와 같이 Id컬럼과 test 컬럼 2개뿐이고 Id는 Identity 전략을 사용했습니다.

```
@Test
public void givenIdentityStrategy() {
    Transaction transaction = session.beginTransaction();

    for (int i = 0; i < 9; i++) {
        session.persist(new RestaurantOrderIdentity());
        log.info("in for loop : {}", i);
    }

    log.info(" commit! ");
    transaction.commit();
}

```
트랜잭션을 시작하고 포문을 9번 돌려서 저장을 한 후 트랜잭션을 커밋합니다.

<p align="center">
  <img src="/images/identifier/identity_strategy_result.png" alt="book" width="1200"/>
</p>  


JPA는 persistence context에 sql문을 모아놓고 transaction이 commit될때 모든 sql문을 flush해서 최적화 효과를 누릴 수 있습니다.  
하지만 위 결과를 보면 persist 할때마다 각각의 insert sql문이 flush 되는것을 볼 수 있습니다.  
이렇게 각각의 insert sql문이 flush 됨으로써 database와 동기화가 된다면
jdbc batch api 최적화를 누릴수 없게 됩니다.  
또한 매번 insert가 실행될때마다 데이터베이스에 동기화(flush)하는 비용도 발생합니다.  

#### Sequence

```
@Entity
public class RestaurantOrderSequence {
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "hilo_sequence_generator")
    @GenericGenerator(
            name = "hilo_sequence_generator",
            strategy = "sequence",
            parameters = {
                    @Parameter(name = "sequence_name", value = "hilo_seqeunce"),
                    @Parameter(name = "initial_value", value = "1"),
                    @Parameter(name = "increment_size", value = "5"),
                    @Parameter(name = "optimizer", value = "hilo")
            }
    )
    private Long id;

    @Column
    private String test;
}
```
위 엔티티는 hilo라는 optimizer를 사용하고 초기 시작값은 1 increment_size는 5로 정했습니다.

```
@Test
public void givenSequenceStrategy() {
    Transaction transaction = session.beginTransaction();

    for (int i = 0; i < 9; i++) {
        session.persist(new RestaurantOrderSequence());
        log.info("in for loop : {}", i);
    }

    log.info(" commit! ");
    transaction.commit();
}
```
위 identity 전략을 사용한 똑같은 소스인데 저장하는 entity만 다릅니다.

<p align="center">
  <img src="/images/identifier/sequence_strategy_result.png" alt="book" width="1200"/>
</p>  

위 결과를 보면 아시겠지만 이번에는 commit 하기 전까지 insert sql문이 flush를 하지 않습니다.  
hibernate가 sequence call을 해서 1-5까지 값을 가져와서 캐쉬에 저장하고 만약 6번째 값이 필요하면
두번째 seqeunce call을 해서 6-10까지의 값을 가져옵니다.  
따라서 9번의 포문이 돌았기 때문에 2번의 seqeunce call을 해서 cache에 저장해놓고
마지막 commit 시점에 한꺼번에 insert sql문을 데이터베이스와 동기화 하는 결과입니다.  
이렇게 하면 JDBC batch api 기능을 사용할 수 있어서 퍼포먼스가 매번 동기화 하는 identity 전략보다 우수합니다.
다만 persistence context에 너무나 많은 sql문을 쌓아놓고 있으면 out of memory 에러가 날수도 있습니다.  


만약 insert문이 소수라면 이 차이는 무시할만한 수준이지만 Spring batch를 사용해서 대용량 데이터를 insert한다면 이 차이는 무시하지 못할 수준이 될겁니다.  

### 결론
<br />
SEQUENCE 전략을 사용하자.

<br />
<br />
<br />
<br />

출처 : [https://vladmihalcea.com/jpa-entity-identifier-sequence/](https://vladmihalcea.com/jpa-entity-identifier-sequence/)  
[https://vladmihalcea.com/hibernate-hidden-gem-the-pooled-lo-optimizer/](https://vladmihalcea.com/hibernate-hidden-gem-the-pooled-lo-optimizer/)  
[https://www.baeldung.com/hibernate-identifiers](https://www.baeldung.com/hibernate-identifiers)
[https://discourse.hibernate.org/t/generated-value-strategy-auto/6481](https://discourse.hibernate.org/t/generated-value-strategy-auto/6481)  
[https://www.baeldung.com/hi-lo-algorithm-hibernate](https://www.baeldung.com/hi-lo-algorithm-hibernate)  
[https://dotnettutorials.net/lesson/difference-between-sequence-and-identity-in-sql-server/](https://dotnettutorials.net/lesson/difference-between-sequence-and-identity-in-sql-server/)
