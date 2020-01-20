#### JPA

- **J**ava **P**ersistent **A**PI
- 자바 어플리케이션에서 관계형 데이터베이스를 사용하는 방식을 정의한 **인터페이스**
  - 라이브러리가 아니다.
- 핵심
  - `EntityManagerFactory`
  - `EntityManager`
  - `EntityTransaction`

#### Hibernate

- JPA의 구현체 중 하나이다.
- Hibernate가 지원하는 메소드 내부에서는 JDBC API가 동작
- ![JPA와 Hibernate의 상속 및 구현 관계](https://suhwan.dev/images/jpa_hibernate_repository/jpa_hibernate_relationship.png)



#### Spring Data JPA

- `Repository`가 Spring Data JPA의 핵심
- `Repository`의 구현에서 JPA를 사용하고 있다.
- `Repository` 인터페이스의 기본 구현체는 `SimpleJpaRepository`
- ![JPA, Hibernate, Spring Data JPA의 전반적인 그림](https://suhwan.dev/images/jpa_hibernate_repository/overall_design.png)



#### ORM

- 객체와 관계와의 설정
  - 객체 : OOP의 객체
  - 관계 : 데이터베이스
- 객체 지향적인 코드로 인해 더 직관적이고 비즈니스 로직에 더 집중할 수 있다.
- application class와 sql 데이터베이스의 테이블 사이의 맵핑 정보를 기술한 메타데이터를 사용하여, application의 class를 sql 데이터베이스의 테이블에 자동으로 영속화 해주는 기술



#### EntityManagerFactory

- `EntityManagerFactory`는 여러 `EntityManager`를 생성하는 객체이다.

- `EntityManagerFactory` 생성시 커넥션 풀도 함께 생성된다.

- 같은 `EntityManagerFactory`를 통해 생성되는 `EntityManager`는 같은 DB에 접속한다.

- 한 번만 생성되며 어플리케이션 전체에서 공유된다.

  - 생산되는 비용이 크다.
  - 여러 스레드가 동시에 접근해도 안전하고 서로 다른 스레드 간에 공유가 가능하다.

- `@PersistenceUnit`를 통해 객체를 얻을 수 있다.

  - `@PersistenceContext`를 통해 `EntityManager`를 주입받아 사용할 수 있기 때문에 잘 사용하지 않는다.

  ```java
  @PersistenceUnit
  EntityManagerFactory entityManagerFactory;
  ```



#### EntityManager

- `Entity`를 저장, 수정, 삭제, 조회 등 `Entity`와 관련된 작업을 수행

- `@PersistenceContext`를 통해 객체를 얻을 수 있다.

  ```java
  @PersistenceContext
  EntityManager entityManager
  ```

  

#### 영속성 컨텍스트(persistence context)

- 영속성 : `Entity`를 영구적으로 저장해주는 환경
  - 영속성을 갖지 않는 데이터는 단지 메모리에만 존재하기 때문에 프로그램을 종료하면 모두 없어진다.
- Application과 DB 사이에 존재하는 논리적인 개념
  - `Entity`의 환경을 저장
- `EntityManager`를 통해서만 접근이 가능하다.
- `flush()` 메소드를 호출하면 `Entity`가 DB에 반영된다.
  - `entityManager.flush()`
  - 트랜잭션의 `commit()`
  - `JPQL Query` 
- 1차 캐시
  - `Entity` 조회시 영속성 컨텍스트에 존재하면 DB 조회를 하지 않고 바로 값을 return
- Transactional write-behind
  - 트랜잭션 `commit()` 될때까지 내부 쿼리 저장소에 모아두고 한번에 쿼리를 실행
- Dirty Checking
  - `Entity`의 snapshot을 유지하면서 `Entity`의 변경사항을 체크한다.
  - `update` 쿼리시 결과가 같다.
- Lazy Loading
  - 특정 Entity와 연관된 Entity는 실제 호출될 때 로딩되도록 지원 (Proxy 사용)



#### Entity 생명주기

![fig](http://www.objectdb.com/files/images/manual/jpa-states.png)

- New : Entity가 생성된 시점
- Managed : 영속성 컨텍스트에서 관리되는 Entity. 식별자를 갖는다.
- Detached : 영속성 컨텍스트에서 존재하다가 분리된 Entity. 식별자를 갖는다.
- Removed : 영속성 컨텍스트와 데이터베이스에서 삭제된 Entity

---

#### datasource

- JDBC 명세의 일부분이며 일반화된 연결 팩토리이다.

- DB와 관계된 connection 정보를 담고 있다.

  - Spring은 Bean으로 등록된 datasource를 이용해 DB와 연결한다.
  - Bean으로 생성시 넘겨주는 인자 예시 : `driverClassName`, `url`, `username`, `password` 등

- 기능

  - DB Server와 연결
  - DB Connection Pooling
    - DB와의 Connection을 미리 생성시켜 저장소에 저장했다가 DB와의 연결 요청이 있으면 저장소에서 꺼내 사용
  - 트랜잭션 처리

  

#### JDBC

- DB에 접근할 수 있도록 Java에서 제공하는 API
- DATABASE----------**JDBC**----------CODE



#### JDBC 대신 도메인 모델 사용하는 이유

- 객체 지향 프로그래밍의 장점을 활용하기 좋다.
- 각종 디자인 패턴
- 코드 재사용 용이
- 비즈니스 로직 구현 및 테스트 용이

---

#### JPA

- java ORM 기술에 대한 API 표준 명세

  - java에서 제공하는 API

- ORM을 사용하기 위한 표준 인터페이스를 모아둔 것

- 구성요소

  - `javax.persistance` 패키지로 정의된 API
  - JPQL
  - 객체/관계 메타데이터

  

#### JPA 구현체

- Hibernate
- EclipseLink
- DataNucleus
- OpenJPA
- ...

#### JPA의 질의 방법

- Criteria
  - java code를 이용해 JPQL을 작성
  - type-safe
- QueryDSL
  - type-safety
- native SQL

---

#### JPQL

- JPA를 구현한 프레임워크에서 사용하는 언어

- JPQL을 SQL로 변환해 데이터베이스에 질의

- 구조

  ```sql
  SELECT ... FROM ...
  [WHERE ...]
  [GROUP BY ... [HAVING ...]]
  [ORDER BY ...]
  ```

  ```sql
  DELETE FROM ... [WHERE ...]
  UPDATE ... SET ... [WHERE ...]
  ```

- 예시

  ```sql
  SELECT u FROM {Class} AS u where u.userid='some_value'
  ```

  - `{Class} as u`와 같이 별칭을 필수적으로 지정해야 한다.

- JPQL 문을 작성하면 `createQuery({JPQL 쿼리}, {반환할 Entity Class type})` 메소드로 쿼리 객체를 생성해야 한다.

---

#### @Transactional

- `RuntimeException`이나 `Error`가 발생하면 rollback 한다.
  - `checked exception` 에는 rollback 하지 않는다.
- readOnly
  - 성능최적화를 해줄 수 있는 여지가 생긴다.
  - 데이터를 변경하는 operation이 없으면 readOnly 값을 true로 설정하는 것을 권장

---

#### Pageable

- 기존의 오브젝트의 목록 가져오는 방식

  ```java
  someRepository.findAll();
  ```

  - 가져올 오브젝트의 양이 많아지면 많아질수록 쿼리 응답시간이 늘어난다.

- jpa repository의 `findAll()` 메소드의 파라미 터중 `Pageable pageable`이 있다.

  - `Pageable` interface를 상속하는 `AbstractPageRequest`를 상속하는 `PageRequest` 클래스로 Pageable 구현

  - 예시

    ```java
    PageRequest pageRequest = PageRequest.of(0,10, Sort.by(Sort.Direction.DESC,"SOME_VALUE"));
    ```

    - `PageRequest` 클래스의 `of` static 메소드를 사용한다.

      ```java
      public class PageRequest extends AbstractPageRequest {
          public static PageRequest of(int page, int size) {
              return of(page, size, Sort.unsorted());
          }
      
          public static PageRequest of(int page, int size, Sort sort) {
              return new PageRequest(page, size, sort);
          }
      
          public static PageRequest of(int page, int size, Direction direction, String... properties) {
              return of(page, size, Sort.by(direction, properties));
          }
      }
      ```

- `pageRequest`의 결과 값으로 `Page<Object>` 객체가 return 된다.

  - Page 객체의 메소드

    ```java
    public interface Page<T> extends Slice<T> {
        static <T> Page<T> empty() {
            return empty(Pageable.unpaged());
        }
        
        static <T> Page<T> empty(Pageable pageable) {
            return new PageImpl(Collections.emptyList(), pageable, 0L);
        }
        
        int getTotalPages();
        long getTotalElements();
        <U> Page<U> map(Function<? super T, ? extends U> var1);
    ```

  - Page 객체가 상속하는 Slice 인터페이스

    ```java
    public interface Slice<T> extends Streamable<T> {
        int getNumber();
        int getSize();
        int getNumberOfElements();
        List<T> getContent();
        boolean hasContent();
        Sort getSort();
        boolean isFirst();
        boolean isLast();
        boolean hasNext();
        boolean hasPrevious();
     
        default Pageable getPageable() {
            return PageRequest.of(this.getNumber(), this.getSize(), this.getSort());
        }
    
        Pageable nextPageable();
        Pageable previousPageable();
        <U> Slice<U> map(Function<? super T, ? extends U> var1);
    
        default Pageable nextOrLastPageable() {
            return this.hasNext() ? this.nextPageable() : this.getPageable();
        }
    
        default Pageable previousOrFirstPageable() {
            return this.hasPrevious() ? this.previousPageable() : this.getPageable();
        }
    }
    
    ```

    

---

#### 참조

- https://hyeooona825.tistory.com/87
- https://happygrammer.tistory.com/149
- [https://kihoonkim.github.io/2017/01/27/JPA(Java%20ORM)/2.%20JPA-%EC%98%81%EC%86%8D%EC%84%B1%20%EA%B4%80%EB%A6%AC/](https://kihoonkim.github.io/2017/01/27/JPA(Java ORM)/2. JPA-영속성 관리/)
- https://gmlwjd9405.github.io/2018/05/15/setting-for-db-programming.html
- https://suhwan.dev/2019/02/24/jpa-vs-hibernate-vs-spring-data-jpa/