## Event-driven architecture

#### Event-driven architecture (EDA)

- event에 대한 생산, 탐지, 소비 및 반응을 촉진하는 소프트웨어 아키텍처 패러다임 (wikipedia)
- 분산된 시스템 간에 event를 생성, 발행하고 발행된 event를 필요로 하는 수신자에게 전송, 필요에 따라 처리하는 시스템 아키텍처
- 특정 행동이 자동으로/순서에 따라 발생하는 것이 아닌 어떤 일에 대한 반응으로 동작하는 디자인 패턴



#### Event?

- 시스템 내, 외부에서 발생한 '주목할 만한 상태의 변화' (change in state)
- Http Request로 event이다.
- ***event-driven***
  - 특정 행동이 자동으로, 혹은 정해진 순서에 따라 발생하는 것이 아닌, 어떤 일에 대한 반응으로 일어나는 구조
  - 컴퓨터 입장에서 보면 event를 발생시켜 자신의 선택을 표현
- event는 다양한 방법으로 publish 된다.
  - 큐에서 이벤트를 고나리하고 적절한 consumer에기 전송
  - publisher/subcriber 모델을 사용하여 event를 publish하고 모든 관련있는 부분으로부터 접근가능하게 한다.
- producer : event를 publish
- consumer : event를 receive



#### EDA의 구성요소

- Event generator
  - 시스템 내, 외부의 상태 변화를 감지하여 표준화된 형식의 이벤트를 생성
- Event channel
  - event generator에서 수집된 정보를 event processing engine까지 전파하는 매커니즘
  - TCP/IP connection
  - event는 큐 안에 정렬되고 event processing engine이 처리할 때까지 대기한다.
- Event processing engine
  - 수신한 이벤트를 식별하고 적절한 처리를 수행
  - 이벤트 처리 결과로 또 다른 이벤트를 발생시킬 수 있다.



#### EDA의 장단점

- 장점
  - Asynchronous
    - event-based 아키텍처는 non-blocking이며 asynchronous이다.
    - 자원들이 업무 단위가 완성되면, 다음에 일어날 일에 대해 걱정하지 않고 다음 업무로 자유롭게 이동할 수 있게 해준다 
  - Decoupling(=Loose Coupling)
    - 시스템 간의 느슨한 결합이 가능 하므로 분산 시스템, microservice 환경에서 시스템 간 의존성을 배제할 수 있다.
  - 다른 시스템의 정보를 알 필요가 없다. (서로 독립적)
  - micro service 단위로 시스템을 분리하기 쉽기 때문에 확장성, 탄력성을 고려하기 쉽다.
- 단점
  - Broker dependency
    - Event를 전송하기 위한 Message Broker에 대한 의존성이 커지기 때문에 Message Broker 장애 상황 시, 전체 장애로 이어질 수 있다.
  - Transaction 단위가 격리되기 때문에 서비스 장애 발생시 retry/rollback을 고려해야 한다.
  - 디버깅이 어렵다.

---

#### Event-Driven Programming

- 이벤트에 의해 결정되는 프로그래밍 패러다임
- 이벤트가 발생할 때 이를 감지하고 적합한 이벤트 핸들러를 사용하여 이벤트를 처리하도록 설계
- 단순히 Listener에 행위만 등록해주면 간편하게 이벤트를 제어할 수 있다.
  - Event handle만이 관심의 대상



#### Spring WebFlux

- Event-Driven을 토대로 만든 프레임워크

- non-blocking I/O를 이용해 많은 동시성 연결을 다룰 수 있다.

- 사용자들에 의해 요청이 들어오면 Event Loop을 통해서 작업이 처리가 된다.

   ![Non-blocking request processing](https://howtodoinjava.com/wp-content/uploads/2019/02/Non-blocking-request-processing.png) 

- 서비스간 호출이 많은 마이크로서비스 아키텍처에 적합

- 기본적으로 서버로 netty를 사용한다.

---

#### Event loop

- 이벤트 처리 방식
  - Event Listener + Event handle thread
    - ui 처리 프레임워크에서 보통 사용
  - Event qeuue + event loop
    - 프레임워크의 구현이 복잡해진다.
    - Event queue는 다수의 thread에서 공유
    - thread 종류에 따라 single/multi thread event loop로 나누어짐
    - 결과를 돌려주는 방식에 따라 Callback 패턴과 Future 패턴으로 나누어짐
- Single Thread Event Loop
  - event handle thread가 1개
  - event 발생 순서에 따라 처리
  - 작업 수행 시간이 길어질 수 있다.

- multi Thread Event Loop
  - event handle thread 여러 개
  - event 발생 순서와 작업 수행 순서가 일치하지 않음
  - multi core CPU 효율적으로 사용
  - Thread 갯수가 지나치게 많아지면 문제 발생
    - OOM 에러 발생
    - 과도한 GC 발생
    - Context switching 비용
    - 공유 자원에서의 thread 경합



#### netty

- 이벤트 loop 기반 프레임 워크
- single thread event loop과 multi thread event loop 기반 프레임 워크 둘다 지원



#### netty's event loop

- 네티의 이벤트는 채널에서 발생한다.
- 이벤트 루프 객체는 각각 이벤트 큐를 가진다.
- 네티의 채널은 하나의 이벤트 루프에 등록됨
  - 이벤트 발생 순서와 수행 순서의 불일치에서 오는 부작용 최소화
- 이벤트 처리를 위해 single thread event executor 사용



#### netty event 실행

- 데이터를 처리하는 입출력은 netty가 이벤트로 관리하기 때문에 이벤트 핸들러만 구현하면 된다.



#### netty 소켓 채널 데이터 수신

1. netty 이벤트 루프가 채널 파이프라인에 등록된 이벤트 핸들러를 가져옴

2. 이벤트 메서드가 구현되어있으면 실행

3. 마지막 이벤트 핸들러에 도달할 때까지 다음 이벤트 핸들러를 가져와서 1, 2 반복
4. 