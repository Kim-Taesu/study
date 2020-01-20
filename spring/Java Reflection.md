#### Java Reflection

- JVM에서 실행되는 애플리케이션의 런타임 동작을 검사하거나 수정할 수 있는 기능
- Class의 내부 정보를 조회하고 조작 할 수 있는 기법
  - Relection 기법을 이용하여 DI를 제공
- 대표적인 기술
  - 스프링 프레임워크, ORM(하이버네이트), jackson 라이브러리
  - 스프링에서는 런타임 시에 개발자가 등록한 빈을 애플리케이션에서 가져와 사용할 수 있게 된다.



#### Java Class 반환 방법

- `class.getSuperClass()` : 슈퍼 클래스를 반환
- `class.getClass()` : 상속된 클래스를 포함하여 모든 공용 클래스, 인터페이스 및 열거형을 반환
- `class.getDeclaredClass()` : 명시적으로 선언된 모든 클래스 및 인터페이스, 열거형을 반환
- `class.getDeclaringClass()` : 클래스에 구성된 클래스(명시적으로 선언된)를 반환
- `class.getEnclosingClass()` : 클래스의 즉시 동봉된 클래스를 반환



#### 단점

-  private 멤버도 접근과 조작이 가능하다.
- Performance의 오버헤드
  - 동적으로 해석되는 유형이포함되어 특정 JVM 최적화를 수행할 수 없다.
  - 성능에 민감한 애플리케이션에서 자주 호출되는 코드에는 사용하지 않아야 한다.

---

#### 참조

- [https://medium.com/msolo021015/%EC%9E%90%EB%B0%94-reflection%EC%9D%B4%EB%9E%80-ee71caf7eec5](https://medium.com/msolo021015/자바-reflection이란-ee71caf7eec5)