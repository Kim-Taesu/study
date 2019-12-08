#### JUnit

- 자바로 단위 테스트를 만들 때 유용하게 사용
- 테스트 하기 위해 main() 메소드에 있던 테스트 코드를 일반 메소드로 옮겨야 한다.
- JUnit 프레임워크가 요구하는 조건
  - 메소드 public으로 선언돼야 한다.
  - 메소드에 `@Test`라는 어노테이션을 붙여야한다.
- `assert` 메소드로 테스트 케이스의 수행 결과를 판별 
  - `assert(예상값, 실제값)`

---

#### JUnit 어노테이션 ([정말 잘 정리되어있는 블로그](https://shlee0882.tistory.com/202))

![junit annotation에 대한 이미지 검색결과](https://www.lambdatest.com/blog/wp-content/uploads/2019/04/flow-chart.png)

`@Test`

- @Test가 선언된 메서드는 테스트를 수행하는 메소드가 된다.
-  jUnit은 각각의 테스트가 서로 영향을 주지 않고 독립적으로 실행됨을 원칙으로 @Test마다 객체를 생성



`@Ignore`

- `@Ignore`가 선언된 메서드는 테스트를 실행하지 않게 한다.



`@Before`

- `@Before`가 선언된 메서드는 `@Test` 메서드가 실행되기 전에 반드시 실행
- `@Test`메서드에서 공통으로 사용하는 코드를 `@Before` 메서드에 선언하여 사용



`@After`

- `@After`가 선언된 메서드는 `@Test` 메소드가 실행된 후 실행



`@BeforeClass`

- `@BeforeClass` 어노테이션은 `@Test` 메소드보다 먼저 한번만 수행되어야 할 경우에 사용



`@AfterClass`

- `@AfterClass` 어노테이션은 `@Test` 메소드 보다 나중에 한번만 수행되어야 할 경우에 사용하면 된다.

---

#### JUnit 코드 형식 예제

```java
import static org.junit.jupiter.api.Assertions.assertEquals;
import example.util.Calculator;
import org.junit.jupiter.api.Test;
class MyFirstJUnitJupiterTests {
    private final Calculator calculator = new Calculator();
    @Test
    void addition() {
        assertEquals(2, calculator.add(1, 1));
    }
}
```

- 조건 - 행위 - 결과

- 조건

  - ```java
    assertEquals(2, calculator.add(1, 1));
    ```

- 행위

  - `calculator.add(1, 1)`실행

- 결과

  - `calculator.add(1, 1)`의 값

---

#### 테스트 주도 개발(TDD)

- 만들고자 하는 기능의 내용을 담고 있으면서 만들어진 코드를 검증도 해줄 수 있도록 테스트 코드를 먼저 만들고, 테스트를 성공하게 해주는 코드를 작성하는 방식의 개발
- 기본 원칙 : `실패한 테스트를 성공시키기 위한 목적이 아닌 코드는 만들지 않는다.`
- 테스트를 작성하고 이를 성공시키는 코드를 만든는 작업의 주기를 가능한 한 짧게 가져가도록 권장

---

#### 스프링 + JUnit

- ```java
  @RunWith(SpringJUnit4ClassRunner.class)
  @ContextConfiguration(locations = "file:web/WEB-INF/spring-config/*.xml")
  ```

  - `@RunWith`으로 `SpringJUnit4ClassRunner`라는 JUnit용 테스트 컨텍스트 프레임워크 확장 클래스를 지정
    - JUnit이 테스트를 진행하는 중에 테스트가 사용할 애플리케이션 컨텍스트를 만들고 관리하는 작업을 진행
  - `@ContextConfiguration`은 자동으로 만들어줄 애플리케이션 컨텍스트의 설정파일 위치를 지정