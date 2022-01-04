## 트랜잭션 애노테이션 (`@Transactional`)

```java
package org.springframework.transaction.annotation;

import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Inherited;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import org.springframework.core.annotation.AliasFor;

// 애노테이션을 사용할 대상을 지정한다. 여기에 사용된 메서드와 타입(클래스, 인터페이스) 처럼 한 개 이상의 대상을 지정할 수 있다.
@Target({ElementType.TYPE, ElementType.METHOD})
// 애노테이션 정보가 언제까지 유지되는지를 지정한다. 이렇게 설정하면 런타임 때도 애노테이션 정보를 리플렉션을 통해 얻을 수 있다.
@Retention(RetentionPolicy.RUNTIME)
// 상속을 통해서도 애노테이션 정보를 얻을 수 있게 한다.
@Inherited
@Documented
public @interface Transactional {

		// 트랜잭션 속성의 모든 항목을 엘리먼트로 지정할 수 있다. 디폴트 값이 설정되어 있으므로 모두 생략 가능하다.

    @AliasFor("transactionManager")
    String value() default "";

    @AliasFor("value")
    String transactionManager() default "";

    Propagation propagation() default Propagation.REQUIRED;

    Isolation isolation() default Isolation.DEFAULT;

    int timeout() default -1;

    boolean readOnly() default false;

    Class<? extends Throwable>[] rollbackFor() default {};

    String[] rollbackForClassName() default {};

    Class<? extends Throwable>[] noRollbackFor() default {};

    String[] noRollbackForClassName() default {};
}
```

- `@Transactional` 애노테이션의 타깃은 메소드와 타입이다.
    - 메소드, 클래스, 인터페이스에 사용할 수 있다.
- `@Transactional` 애노테이션을 트랜적션 속성정보로 사용하도록 지정하면 스프링은 @Transactional이 부여된 모든 오브젝트를 자동으로 타깃 오브젝트로 인식한다.
    - 이 때 사용되는 포인트컷은 `TransactionAttributeSourcePointcut` 이다.
    - `TransactionAttributeSourcePointcut` 은 스스로 표현식과 같은 선정기준을 갖고 있지 않다.
    - 대신 `@Transactional` 이 타입 레벨이든 메소드 레벨이든 상관없이 부여된 빈 오브젝트를 모두 찾아서 포인트컷의 선정 결과로 돌려준다.
- `@Transactional` 은 기본적으로 트랜잭션 속성을 정의하는 것이지만, 동시에 포인트컷의 자동등록에도 사용된다.

## 트랜잭션 속성을 이용하는 포인트컷

- `TransactionAttributeSourcePointcut`는  메소드 이름 패턴을 통해 부여되는 일괄적인 트랜잭션 속성정보 대신 `@Transactional` 애노테이션의 엘리먼트에서 트랜잭션 속성을 가져오는 `AnnotationTransactionAttributeSource`를 사용한다.
- `@Transactional` 은 메소드마다 다르게 설정할 수도 있으므로 매우 유연한 트랜잭션 속성 설정이 가능해진다.
- 동시에 포인트컷도 `@Transactional` 을 통한 트랜잭션 속성정보를 참조하도록 만든다.
- `@Transactional`로 트랜잭션 속성이 부여된 오브젝트라면 포인트컷의 선정 대상이기도 하기 때문이다.
- 따라서 포인트컷과 트랜잭션 속성을 애노테이션 하나로 지정할 수 있다.
- 트랜잭션 부가기능 적용 단위는 메소드다.
    - 메소드마다 `@Transactional` 을 부여하고 속성을 지정할 수 있다.
    - 유연한 속성 제어는 가능하겠지만, 코드는 지저분해지고, 동일한 속성 정보를 가진 애노테이션을 반복적으로 메소드마다 부여해주는 바람직하지 못한 결과를 가져올 수 있다.
- aop의 Advice로는 `TxInterceptor`를 사용한다.

## 대체 정책

- 스프링은 `@Transactional`을 적용할 때 4단계의 대체(fallback) 정책을 이용하게 해준다.
    - 메소드의 속성을 확인할 때 `타깃 메소드`, `타깃 클래스`, `선언 메소드`, `선언 타입(클래스, 인터페이스)` 순서에 따라 `@Transactional`이 적용되었는지 차례로 확인하고, 가장 먼저 발견되는 속성정보를 사용하게 하는 방법

```java
// 인터페이스 타입 (우선순위 4번)
public interface Service {
	// 인터페이스 메서드 (우선순위 3번)
	void method1();
	// 인터페이스 메서드 (우선순위 3번)
	void method2();
}

// 타깃 클래스 (우선순위 2번)
public class ServiceImpl implements Service {
	// 타깃 오브젝트의 메서드 (우선순위 1번)
	public void method1(){};
	// 타깃 오브젝트의 메서드 (우선순위 1번)
	public void method2(){};
}
```

- 타깃 오브젝트의 메소드가 여러개면 타깃 클래스에 `@Transactional`을 부여하는 것이 편리하다.
- 특정 메소드만 타깃 클래스에 선언한 `@Transactional` 의 공통 속성을 따르지 않는다면 해당 메소드에만 추가로 `@Transactional` 를 부여해주면 된다.
    - 타깃 클래스의 메서드에 붙은 `@Transactional` 가 가장 우선이기 때문에 해당 메소드는 메소드 레벨의 `@Transactional` 속성을 사용할 것이다.
- 기본적으로 `@Transactional` 적용 대상은 클라이언트가 사용하는 인터페이스가 정의한 메소드이므로 `@Transactional` 도 타깃 클래스보다는 인터페이스에 두는게 바람직하.
- 하지만 인터페이스를 사용하는 프록시 방식의 AOP가 아닌 방식으로 트랜잭션을 적용하면 인터페이스에 정의한 `@Transactional` 은 무시되기 때문에 안전하게 타깃 클래스에 `@Transactional` 을 두는 방법도 권장한다.
- 프록시 방식 AOP의 종류와 특징, 또는 비 프록시 방식 AOP의 동작원리를 잘 이해하고 있고 그에 따라 `@Transactional` 의 적용 대상을 적절하게 변경해줄 확신이 있거나, 반드시 인터페이스를 사용하는 타깃에만 트랜잭션을 적용하겠다는 확신이 있다면 인터페이스에 `@Transactional` 을 적용하면 된다.
    - 아니라면 마음 편하게 타깃 클래스와 타깃 메소드에 적용하는 편이 낫다.

## `PlatformTransactionManager`

- 스프링 트랜잭션 추상화의 핵심 인터페이스
- 모든 스프링의 기능과 코드는 이 인터페이스를 통해서 로우레벨의 트랜잭션 서비스를 이용할 수 있다.
- 트랜잭션 경계를 지정하는 데 사용한다.
    - 트랜잭션이 어디서 시작하고 종료하는지, 종료할 때 정상 종료(커밋)인지 비정상 종료(롤백)인지를 결정하는 것이다.
- 스프링에서는 시작과 종료를 트랜잭션 전파 기법을 이용해 자유롭게 조합하고 확장할 수 있다.
    - 때문에 적절한 트랜잭션을 가져온다는 의미의 `getTransaction()` 메서드를 사용한다.
- `getTransaction()` 은 트랜잭션 속성에 따라서 새로 시작하거나 진행 중인 트랜잭션에 참여하거나, 진행 중인 트랜잭션을 무시하고 새로운 트랜잭션을 만드는 식으로 상황에 따라 다르게 동작한다.
    - `TransactionDefinition` 은 트랜잭션의 네 가지 속성을 나타내는 인터페이스이다.
    - `TrasactionStatus` 는 현재 참여하고 있는 트랜잭션 ID와 구분정보를 담고 있다.
        - 커밋 또는 롤백 시에 이 `TrasactionStatus` 를 사용한다.
- 선언 트랜잭션 방식을 사용할 것이라면 사실 `PlatformTransactionManager` 인터페이스의 사용 방법은 몰라도 상관 없다.
    - 다만 이 추상화된 인터페이스를 구현한 트랜잭션 서비스 클래스의 종류를 알고 적절한 것을 선택해서 빈으로 등록하는 방법만 알고 있으면 된다.

```java
package org.springframework.transaction;

import org.springframework.lang.Nullable;

public interface PlatformTransactionManager extends TransactionManager {

	TransactionStatus getTransaction(@Nullable TransactionDefinition definition)
			throws TransactionException;

	void commit(TransactionStatus status) throws TransactionException;

	void rollback(TransactionStatus status) throws TransactionException;

}
```

## `DataSourceTransactionManager`

- Connection의 트랜잭션 API를 이용해서 트랜잭션을 관리해주는 트랜잭션 매니저다.
- 이 트랜잭션 매니저를 사용하려면 트랜잭션을 적용할 DataSource가 스프링의 빈으로 등록돼야 한다.
- `DataSourceTransactionManager` 를 빈으로 등록할 때는 트랜잭션을 적용할 DAO가 사용하는 것과 동일한 DataSource를 빈으로 제공해줘야 한다.
- `DataSourceTransactionManager` 가 사용할 DataSource는 getConnection()이 호출될 때마다 매번 새로운 Connection을 돌려줘야 한다.
    - ThreadLocal 등을 이용해 틀내잭션을 저장해두고 돌려주는 특별한 기능을 가진 DataSource를 사용하면 안 된다.

## 트랜잭션 애노테이션 사용을 위한 설정

- `@EnableTransactionManagement` 사용
    - 어노테이션 기반 트랜잭션 허용 방법
    
    ```java
    @Configuration
    @EnableTransactionManagement
    public class AppConfig {
    
       @Bean
       public FooRepository fooRepository() {
           // configure and return a class having @Transactional methods
           return new JdbcFooRepository(dataSource());
       }
    
       @Bean
       public DataSource dataSource() {
           // configure and return the necessary JDBC DataSource
       }
    
       @Bean
       public PlatformTransactionManager txManager() {
           return new DataSourceTransactionManager(dataSource());
       }
    }
    ```
    
- `@EnableTransactionManagement` 는 컨테이너의 모든  `PlatformTransactionManager` 빈에 대해 `transactionManager`를 찾기 때문에 직관적으로 `TransactionManagementConfigurer`를 구현해서 `annotationDrivenTransactionManager` 메서드에 사용할 `transactionManager`를 명시해도 된다.
    
    ```java
    @Configuration
    @EnableTransactionManagement
    public class AppConfig implements TransactionManagementConfigurer {
    
       @Bean
       public FooRepository fooRepository() {
           // configure and return a class having @Transactional methods
           return new JdbcFooRepository(dataSource());
       }
    
       @Bean
       public DataSource dataSource() {
           // configure and return the necessary JDBC DataSource
       }
    
       @Bean
       public PlatformTransactionManager txManager() {
           return new DataSourceTransactionManager(dataSource());
       }
    
       @Override
       public PlatformTransactionManager annotationDrivenTransactionManager() {
           return txManager();
       }
    }
    ```
    

## 트랜잭션 추상화와 동기화

- 스프링이 제공하는 트랜잭션 서비스는 `트랜잭션 추상화`와 `트랜잭션 동기화` 두 가지로 생각해볼 수 있다.
- 스프링은 데이터 액세스 기술과 트랜잭션 서비스 사이의 종속성을 제거하고 스프링이 제공하는 트랜잭션 추상 계층을 이용해서 트랜잭션 기능을 활용하도록 만들어준다.
    - 이를 통해 트랜잭션 서비스의 종류나 환경이 바뀌더라도 트랜잭션을 사용하는 코드는 그대로 유지할 수 있는 유연성을 얻을 수 있다.
- 스프링의 트랜잭션 동기화는 트랜잭션을 일정 범위 안에서 유지해주고, 어디서든 자유롭게 접근할 수 있게 만들어준다.
    - 트랜잭션 동기화는 트랜잭션 추상화, 데이터 액세스 기술을 위한 템플릿과 더불어 선언적 트랜잭션을 가능하게 해주는 핵심 기능이다.

## 트랜잭션 경계설정 전략

- 트랜잭션 매니저를 빈으로 등록하고 JdbcTemplate이나 스프링 트랜잭션과 연동되는 EntityManager 또는 Session을 사용하도록 DAO 코드를 작성했다면 다음은 트랜잭션의 경계를 설정하는 작업을 할 차례이다.
- 트랜잭션의 시작과 종료가 되는 경계는 보통 서비스 계층 오브젝트의 메소드다.
    - 비즈니스 로직이 거의 없어서 서비스 계층과 데이터 액세스 계층을 통합했다면, 통합된 계층의 메소드가 트랜잭션 경계가 될 것이.

## 선언적 트랜잭션 경계 설정

- 선언전 트랜잭션을 이용하면 코드에는 전혀 영향을 주지 않으면서 특정 메소드 실행 전후에 트랜잭션이 시작되고 종료되거나 기존 트랜잭션에 참여하도록 만들 수 있다.
- 선언적 트랜잭션 경계설정은 트랜잭션 프록시 빈 덕분에 가능한 것이다.

### AOP와 tx 네임스페이스

- 트랜잭션 경계 설정이라는 부가기능을 AOP를 이용해 빈에게 적용하려면 두 가지 정보가 필요하다.
- AOP는 부가 기능을 빈 오브젝트에게 적용하는 방법이다.
    - 따라서 어떤 부가기능을 사용할지 결정해야 한다.
    - AOP 용어로 하자면 어드바이스가 필요하다.
    - 트랜잭션 경계설정 부가기능이므로 트랜잭션 어드바이스가 필요하다.
- 어떤 대상에게 이 부가기능을 부여할지를 선정해야 한다.
    - 선정 대상을 결정하는 기준이 필요하다.
    - AOP 용어로 포인트컷이라고 한다.
- 따라서 트랜잭션 어드바이스와 포인트컷을 결합해서 하나의 AOP 모듈을 정의한다.
    - AOP 모듈을 스프링에서는 어드바이저라고 부른.

## 프록시 모드: 인터페이스와 클래스

- 스프링의 AOP는 기본적으로 다이나믹 프록시 기법을 이용해 동작한다.
- 다이나믹 프록시를 적용하려면 인터페이스가 있어야 한다.
- 인터페이스를 구현하지 않은 클래스에 트랜잭션을 적용해야 하는 경우, 스프링이 지원하는 클래시 프록시 모드를 사용하면 된다.
    - 클래스 프록시는 일반적으로 인터페이스를 구현하지 않는 클래스에 주로 사용된다.
    - 클래스 프록시는 final 클래스에는 적용할 수 없다.
        - 클래스 프록시는 타깃 클래스를 상속해서 프록시를 만드는 방법을 사용하기 때문에 상속이 불가능한 final 클래스에는 적용되지 않는다.
    - 클래스 프록시를 적용하면 클래스의 생성자가 두 번 호출된다.
        - 상속을 통해 프록시를 만들기 때문에 발생하는 현상인데, 이 때문에 생성자에서 리소스를 할당하는 것 같은 중요한 작업은 피하도록 해야 한다.
    - 클래스 프록시 방식을 사용하면 클래스의 모든 public 메서드에 트랜잭션이 적용된다.
        - 아무런 DB 작업이 없는 메서드에도 적용되기 때문에 시간과 리소스에 낭비가 발생한다.

## AOP 방식: 프록시와 AspectJ

- 스프링의 AOP는 기본적으로 프록시 방식이다.
- 프록시 오브젝트를 타깃 오브젝트 앞에 두고 호출 과정을 가로채서 트랜잭션과 같은 부가적인 작업을 진행해준다.
- 스프링의 프록시 AOP 대신 AOP 전용 프레임워크인 AspectJ의 AOP를 사용할 수 있다.
    - AspectJ AOP는 스프링과 달리 프록시를 타깃 오브젝트 앞에 두지 않는다.
    - 대신 타깃 오브젝트 자체를 조작해서 부가기능을 직접 넣는 방식이다.
- 프록시는 기능을 사용하려는 오브젝트인 클라이언트와 서비스를 제공하는 오브젝트 타깃 오브젝트 사이에 데코레이터 패턴을 써서 투명하게 추가된다.
    - 투명하다는 건, 프록시가 추가되더라도 클라이언트와 타깃 오브젝트의 코드를 수정할 필요도 없고 기본 기능에도 영향을 주지 않는다란 뜻이다.
    - 프록시는 스프링 DI의 도움을 받아서 클라이언트와 타깃 오브젝트 사이에 추가되고, 어드바이스가 제공해주는 부가적인 기능을 타깃 오브젝트의 메소드 호출 전후에 실행해준다.
- 프록시가 적용되면 클라이언트 프록시를 타깃 오브젝트라고 생각하고 프록시의 메서드를 호출한다.
    - 프록시는 클라이언트로부터 요청을 받으면 타깃 오브젝트의 메서드로 위임해준다.
    - 타깃 오브젝트에 위임하는 과정에서 부가작업을 추가할 수 있다.
    - 트랙잭션 AOP에 의해 추가된 프록시라면 타깃 오브젝트 메소드 호출 전에 트랜잭션을 시작하고 호출 후에 트랜잭션을 커밋하거나 롤백해줄 것이다.
- 프록시는 클라이언트가 타깃 오브젝트를 호출하는 과정에서만 동작한다는 점을 주목하자
    - 타깃 오브젝트의 메소드가 자기 자신의 다른 메소드를 호출할 때는 프록시를 거치지 않는다.
        - 이미 프록시를 거쳐서 타기 오브젝트까지 작업이 진행됐으므로 타깃 오브젝트에서 자신의 메소드를 호출할 때는 프록시를 거치지 않는다.
- Spring boot는 기본적으로 프록시 생성을 할 때 CGLib를 사용하고 있다.
    - 인터페이스를 구현하지 않고 해당 구현체를 상속받는 방법으로 프록시를 생성한다.
    - Spring은 JDK Proxy를 사용하고 있다.
        - 내부적으로 Relection을 사용하기 때문에 고비용 작업
        - AOP를 적용하기 위해서 반드시 인터페이스를 구현해야 한다.

## 트랜잭션 속성

### 트랜잭션 전파: propagation

- 이제 트랜잭션을 시작하거나 기존 트랜잭션에 참여하는 방법을 결정하는 속성이.
- 틀내잭션 경계의 시작 지점에서 트랜잭션 전파 속성을 참조해서 해당 범위의 트랜잭션을 어떤 식으로 진행시킬지 결정할 수 있다.
- REQUIRED
    - 디폴트 속성이다.
    - 모든 트랜잭션 매니저가 지원하며, 대개 이 속성이면 충분하다.
    - 미리 시작된 트랜잭션이 있으면 참여하고 없으면 새로 시작한다.
    - 하나의 트랜잭션이 시작된 후에 다른 트랜잭션 경계가 설정된 메소드를 호출하면 자연스럽게 같은 트랜잭션으로 묶인다.
- SUPPORTS
    - 이미 시작된 트랜잭션이 있으면 참여하고 그렇지 않으면 트랜잭션 없이 진행한다.
- MANDATORY
    - REQUIRED와 비슷하게 이미 시작된 트랜잭션이 있으면 참여한다.
    - 반면에 트랜잭션이 시작된 것이 없으면 예외를 발생시킨다.
- REQUIRES_NEW
    - 항상 새로운 트랜잭션을 시작한다.
- NOT_SUPPORTED
    - 트랜잭션을 사용하지 않게 한다.
    - 이미 진행 중인 트랜잭션이 있으면 보류시킨다.
- NEVER
    - 트랜잭션을 사용하지 않도록 강제한다.
    - 이미 진행중인 트랜잭션도 존재하면 안 된다.
- NESTED
    - 이미 진행 중인 트랜잭션이 있으면 중첩 트랜잭션을 시작한다.
        - 트랜잭션 안에 다시 트랜잭션을 만든는 것
    - 중첩된 트랜잭션은 먼저 시작된 부모 트랜잭션의 커밋과 롤백에는 영향을 받지만 자신과 커밋과 롤백은 부모 트랜잭션에게 영향을 주지 않는다.

### 트랜잭션 격리수준 : isolation

- 트랜잭션 격리 수준은 동시에 여러 트랜잭션이 진행될 때에 트랜잭션의 작업 결과를 여타 트랜잭션에게 어떻게 노출할 것인지를 결정하는 기준이다.
- DEFAULT
    - 사용하는 데이터 액세스 기술 또는 DB 드라이버 디폴트 설정을 따른다.
    - 보통 드라이버의 격리수준는 DB의 격리수준을 따르는게 일반적이다.
    - 대부분의 DB는 READ_COMMITED를 기본 격리수준으로 갖는다.
- READ_UNCOMMITTED
    - 가장 낮은 격리수준
    - 하나의 트랜잭션이 커밋되기 전에 그 변화가 다른 트랜잭션에 그대로 노출되는 문제가 있다.
    - 성능을 극대화할 때 의도적으로 사용
- READ_COMMITTED
    - 실제로 가장 많이 사용되는 격리수준.
    - 다른 트랜잭션이 커밋하지 않은 정보는 읽을 수 없다.
    - 대신 하나의 트랜잭션이 읽은 로우를 다른 트랜잭션이 수정할 수 있다.
- REPEATABLE_READ
    - 하나의 트랜잭션이 읽은 로우를 다른 트랜잭션이 수정하는 것을 막아준다.
    - 새로운 로우를 추가하는 것은 제한하지 않는다.
- SERIALIZABLE
    - 가장 강력한 트랜잭션 격리수준
    - 트랜잭션을 순차적으로 진행시켜주기 때문에 여러 트랜잭션이 동시에 같은 테이블의 정보를 액세스하지 못한다.
    

### 트랜잭션 제한시간: timeout

- 트랜잭션에 제한시간을 지정할 수 있다.
- 값은 초 단위로 지정한다.
- 디폴트는 트랜잭션 시스템의 제한시간을 따르는 것이다.

### 읽기전용 트랜잭션: read-only, readOnly

- 트랜잭션을 읽기전용으로 설정할 수 있다.
- 성능을 최적화하기 위해 사용할 수도 있고 특정 트랜잭션 작업 안에서 쓰기 작업이 일어나는 것을 의도적으로 방지하기 위해 사용할 수도 있다.
- 트랜잭션을 준비하면서 읽기전용 속성이 트랜잭션 매니저에게 전달된다.
    - 그에 따라 트랜잭션 매니저가 적절한 작업을 수행한다
- 일반적으로는 읽기전용 트랜잭션이 시작된 이후 INSERT, UPDATE, DELETE 같은 쓰기 작업이 진행되면 예외가 발생한다.

### 트랜잭션 롤백 예외: rollback-for, rollbackFor, rolbackForClassName

- 선언전 트랜잭션에서는 런타임 예외가 발생하면 롤백한다.
    - 반면에 예외가 번혀 발생하지 않거나 체크 예외가 발생하면 커밋한다.

### 트랜잭션 커밋 예외: no-rollback-for, noRollbackFor, noRollbackForClassName

- 기본적으로는 롤백 대상인 런타임 예외를 트랜잭션 커밋 대상으로 지정해준다.
