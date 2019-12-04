#### IoC

- 일반적이고 전형적인 뜻 : 제어의 역전
- 스프링을 쓰기 전
  - `개발자`가 프로그램의 흐름(애플리케이션 코드)을 제어하는 주체
- 스프링 사용 이후
  - `프레임워크`가 프로그램의 흐름을 제어한다.
- **제어권이 컨테이너로 넘어가게 되어 제어권의 흐름이 바뀜(IoC)**
  - 스프링 컨테이너가 애플리케이션의 흐름을 제어하는 권한이 없다면 `@Autowired`를 통한 의존성 주입(DI)을 할 수 없다.

---

#### DI

- 일반적이고 전형적인 뜻 : 의존성 주입

- 클래스 사이의 의존관계를 빈 설정 정보를 바탕으로 컨테이너가 자동적으로 연결해주는 것

  - 컨테이너가 애플리케이션 흐름의 주체가 되어서 애플리케이션 코드에 의존관계를 주입해주는 것

- 개발자들은 제어를 담당할 필요없이 빈 설정 파일에 의존 관계가 필요하다는 정보만 추가해주면 된다.

- 오브젝트 레퍼런스를 `외부 Container`로부터 주입 받아서, 실행 시에 동적으로 의존관계가 생성

- 의존성 예제 => [좋은 참고 사이트]([https://mo-world.tistory.com/entry/IOC%EC%99%80-DI-%EC%97%90-%EB%8C%80%ED%95%98%EC%97%AC-%EC%8A%A4%ED%94%84%EB%A7%81-%EA%B0%9C%EB%85%90-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-%EC%89%BD%EA%B2%8C-%EC%84%A4%EB%AA%85](https://mo-world.tistory.com/entry/IOC와-DI-에-대하여-스프링-개념-이해하기-쉽게-설명))

  ```java
  public class HelloVersion1{
  	public void printHello(){
  		System.out.println("hello verion 1")
  	}
  }
  
  public class HelloVersion2{
  	public void printHello(){
  		System.out.println("hello verion 2")
  	}
  }
  
  public class HelloWorld{
  	public static void main(String args[]){
          /** HellWorl 클래스는 HelloVersion1과 HelloVersion2에 의존성을 가지고 있다. **/
          /** 각 클래스의 printHello() 메소드를 실행시키기 위해 각 객체를 따로 생성해줘야 한다. **/
  		HelloVersion1 helloBean = new HelloVersion1;
           // HelloVersion2 helloBean = new HelloVersion2;
  		helloBean.printHello();
  	}
  }
  ```

  - 의존성을 해결하기 위해 객체의 인스턴스를 외부에서 생성해야 한다.

- 의존성 주입의 종류

  - 생성자를 이용한 의존성 주입
  - setter 메소드를 이용한 의존성 주입
    - 스프링이 택한 방식
  - 초기화 인터페이스를 이용한 의존성 주입

- 런타임에 DI가 발생한다.

---

#### IoC/DI 적용

- 사용할 객체들을 컨테이너에 등록한다.
- 애플리케이션 코드에서 해당 객체를 setter 함수의 매개변수로 받아와서 실행 시에 동적으로 의존관계를 설정
  - container의 bean 설정에서 동적으로 구현 클래스를 정해 줄 수 있다.
- 객체를 생성할 때에, 해당 객체가 참조하고 있는 다른 객체에 대한 종속성을 애플리케이션 코드 외부(Container)에 설정하게 함으로써 `결합도는 낮추`면서 `유연성과 확장성은 확장`시킨다.

---

#### 스프링

- IoC / DI 프레임워크
- 유연하고 다양한 기능으로 컨테이너를 사용할 수 있다.
- 스프링 IoC 컨테이너가 관리하는 객체를 `빈(bean)`이라고 한다.
  - 컨테이너를 bean을 관리한다는 의미로 `빈 팩토리(bean factory)`라고 한다.
  - 팩토리 디자인 패턴 구현

#### 애플리케이션 컨텍스트

- DI 기능에 엔터프라이즈 애플리케이션을 개발하는 데 필요한 여러 가지 컨테이너 기능을 추가하여 만든 컨테이너
  - 스프링의 DI Container는 단순한 DI 작업보다 더 많은 일을 한다.
- 스프링에서는 컨테이너를 일반적으로 구성정보를 담아 xml로 구성

---

#### 참조 사이트

- http://www.nextree.co.kr/p11247/
- [https://mo-world.tistory.com/entry/IOC%EC%99%80-DI-%EC%97%90-%EB%8C%80%ED%95%98%EC%97%AC-%EC%8A%A4%ED%94%84%EB%A7%81-%EA%B0%9C%EB%85%90-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-%EC%89%BD%EA%B2%8C-%EC%84%A4%EB%AA%85](https://mo-world.tistory.com/entry/IOC와-DI-에-대하여-스프링-개념-이해하기-쉽게-설명)