#### non-blocking back pressure

- 시스템 리소스를 효율적으로 사용하면서 메시지의 publisher와 subcriber 간의 처리 속도를 조정하는 기능
- 기본적으로 생산자가 빠른 속도로 전송하는 메시지는 처리가 느린 소비자에게는 잠재적으로 문제를 일으킬 수 있다.
  - back-pressure를 조절하지 못하는 경우
- 비동기 처리 모델의 필수 요소이며 비동기 메시지 전달을 통해 구현된다.
- back pressure의 부재는 결국 Out of Memory Exception 발생



---

#### Reactive Streams

- non-blocking back pressure으로 비동기 스트림 프로세싱의 표준을 제공
- 수신측이 송신측에게 압도되지 않고 비동기로 스트림 데이터를 교환

---

#### Reactive Systems

- 배경
  - 현재 애플리케이션은 모바일 기기에서 부터 수천 개의 멀티코어 프로세서에서 동작하는 클라우드 기반의 클러스터까지, 모든 기기에 배포되고 있다.
  - 클라이언트는 밀리초의 응답 시간과 100% 가동률을 기대
- 정의 및 특징
  - 응답이 잘 되고, 탄력적이며 유연하고 메시지 기반으로 동작하는 시스템이다.
  - 기존 시스템보다 유연하고, 느슨한 결합, 확장성이 있다.
  - 높은 응답성을 가지며 클라이언트에게 빠른 피드백 제공
- 특징
  - 응답성 
    - 신속하고 일관성 있는 응답 시간을 제공하고, 신뢰할 수 있는 상한선을 설정하여 일관된 서비스 품질을 제공
    - 오류 처리를 단순화, 일반 사용자에게 신뢰를 조성하고, 새로운 상호작용을 촉진
  - 탄력성
    - 시스템이 장애애 직면하더라도 응답성을 유지 하는 것
  - 유연성
    - 시스템이 작업량이 변화하더라도 응답성을 유지하는 것
    - 입력 속도의 변화에 따라 입력에 할당된 자원을 증가시키거나 감소시키면서 변화에 대응
    - 시스템에서 경쟁하는 지점이나 중앙 집중적인 병목 현상이 존재하지 않도록 설꼐하여, 구성요소를 샤딩하거나 복제하여 입력을 분산시키는 것
  - 메시지 구동
    - reactive system은 비동기 메시지 전달에 의존
    - 컴포넌트사이에서 느슨한 결합, 격리, 위치 투명성을 보장하는 경계를 형성한다.
    - 명시적인 메시지 전달은 시스템에 메시지 큐를 생성하고, 모니터링하며 필요시 back-pressure을 적용함으로써 유연성을 부여하고, 부하 관리와 흐름제어를 가능하게 한다.

 ![img](https://www.reactivemanifesto.org/images/reactive-traits-ko.svg) 

---



## Spring 5

#### WebFlux

- fully non-blocking
- support Reactive Streams back pressure
- Netty, Undertow, Servlet 3.1+ containers와 같은 서버에서 실행



#### MVC vs WebFlux

- Spring MVC는 Java EE의 Servlet spec에 기반하여 만들어져 있고, 이는 blocking, synchronous 방식이다.
  - 비동기 처리 기능이 프레임워크 3에서 추가 되었지만 서블릿은 응답을 기다리는 동안 pool의 쓰레드들을 여전히 지연시킬 수 있다.
- Spring WebFlux는 



#### 왜 WebFlux ?

- non-blocking web stack이 더 적은 스레드로 동시 처리하고 더 적은 하드웨어 리소스로 확장 가능해야 한다.
- 함수형 프로그래밍 사용
- 동시 접속을 대량 처리할 수 있는  fully asynchronous and non-blocking reactive web stack 



- ##### Reactive Stack

  - Spring WebFlux는 멀티 코어, 다음 세대의 프로세서들을 이용하고 수많은 양의 동시접속을 처리하기 위한 웹 프레임 워크
  - Netty, Servlet 3.1+ Containers 사용
  - Reactive Streams Adapters

- ##### Servlet stack

  - Spring MVC는 Servlet API를 기반으로 하며 동기적 blocking I/O 아키텍처를 사용(하나의 request에 하나의 쓰레드)
  - Servlet Containers 사용
  - Servlet API

- ##### 둘 중 뭐가 더 좋다가 아닌 use case에 따라 다르다



---



#### Refference

-  https://www.reactivemanifesto.org/ko 
-  https://atin.tistory.com/574 
-   https://tech.peoplefund.co.kr/2017/08/02/non-blocking-asynchronous-concurrency.html 
-   https://homoefficio.github.io/2017/02/19/Blocking-NonBlocking-Synchronous-Asynchronous/ 
-   https://ozt88.tistory.com/21 



