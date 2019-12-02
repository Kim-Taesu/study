#### 연산자 오버로딩

- primitive 타입에도 메서드가 있다.

  - 아래 두 식은 같다.
  - 인자가 하나뿐인 메서드의 경우 중위 표기법에서 마침표와 괄호를 생략할 수 있다.

  ```scala
  1+2
  1.+(2)
  ```

- 인자가 없는 메서드는 항상 마침표 없이 호출할 수 있다.

  ```scala
  1 toString
  ```

#### if 문

- 표면적으로 스칼라의 if 문은 자바의 if 문과 비슷하다.
- 스칼라의 if 문은 if 식의 결과 값을 다른 변수에 저장할 수 있다.

#### for 문

- for 내장 또는 for 식이라 부른다.

  ```scala
  val list = List("a","b","c")
  for (l <- list) println(l)
  ```

#### 제네레이터 식

- 앞 예제의 l <- list 식

- 컬렉션에서 개별 값을 생성

  - 왼쪽 화살표 연산자(<-)는 List와 같은 컬렉션을 순회하기 위해 사용

- Range를 사용

  ```scala
  for (i < 1 to 10) println(i)
  ```

- List의 원소에 Option값이 들어있으면 제네레이터 식을 했을 때 None은 건너 뛴다.

  - Some()으로 받으면 Some 값만 들어온다.

    ```scala
    val list = List(None, Some("a"), None, Some("b"))
    
    val someValue = for {
         | Some(l) <- list
         | } yield l
    someValue: List[String] = List(a, b)
    ```

#### yield

- for 식에서 새로운 컬렉션을 만든다.
- 괄호 대신 중괄호 사용
- for-yield 식의 결과 컬렉션의 타입은 반복을 수행한 대상 컬렉션의 타입으로부터 추론된다.

#### 문자열 인터폴레이션

- ```scala
  s"foo ${bar}""
  ```

- 위 문자열에는 bar라는 식의 값이 들어간다.

  - bar라는 식이 String과 다른 타입의 인스턴스를 반환한다면 toString 메서드를 호출할 것이다.
  - String으로 변환할 수 없으면 오류가 발생
  - bar가 단순한 변수 참조라면 중괄호 생략 가능

#### 트레이트

- 자바의 인터페이스 기능
  - 메서드를 선언하면서 원하면 정의까지 할 수 있는 인터페이스
- 트레이트에 인스턴스 필드를 선언하고 원한다면 정의까지 할 수 있다.
- 트레이트를 혼합하기 위해 with 키워드 사용