## select vs poll

#### file descriptor

- 프로세스가 파일을 다룰 때 사용하는 개념
- 특정 파일에 접근하기 위한 추상적인 키
- 0이 아닌 정수값을 가진다.
- 프로세스가 read(), write() 함수를 수행할 때 인자로 사용되며, 어느 파일에 read(), write() 요청을 수행할 지를 구분하는 값으로 사용된다.
- 0, 1, 2번 파일 디스크립터는 특수한 목적으로 사용
  - 0번 : 표준입력
  - 1번 : 표준 출력
  - 2번 : 표준 오류



#### select

- 사용이 쉽고 지원 OS가 많아 이식성이 좋다.
- 등록된 파일 디스크립터를 하나하나 체크 하고 커널과 유저 공간 사이에 여러번의 데이터 복사가 있음
- 관리 파일 디스크립터 수에 제한이 있다.
- 한계점
  - 관리하는 파일 디스크립터의 수가 증가하면 성능이 떨어진다.
  - 관리할 수 있는 파일 디스크립터의 수가 한정되어 있기 때문에 그 수를 초과하면 사용할 수 없다.



#### poll

- 관리 파일 디스크립터의 수가 무제한
- system call의 호출이 select보다 적다
- 접속수가 늘어나면 파일 디스크립터당 체크 마스크의 크기가 select에 비해 많으므로 성능이 select 보다 떨어짐







---

## Asynchronous Non-blocking I/O Model



#### synchronous 

- 작업을 요청한 후 작업의 결과가 나올 때까지 기다린 후 처리
  - 프로세스는 커널에 지속적으로 I/O 준비사항을 체크



#### Asynchronous 

- 사전적 의미 : 동시에 존재하거나 발생하지 않는
- 클라이언트에서 서비스로 전송된 요청이 이후 임의의 시점에 처리된다.
- 클라이언트는 서비스 내에서 실행되는 것을 직접 보거나 동기화할 수 없다.
  - 서비스가 요청에 대한 처리를 마쳐야만 클라이언트가 재개되는 동기 처리의 반대의미



#### Synchronous/Asynchronous

- 호출되는 함수의 작업 완료 여부를 누가 신경쓰냐가 관심사
- Asynchronous
  - 호출되는 함수에게 callback을 전달해서, 호출되는 함수의 작업이 완료되면 호출되는 함수가 전달받은 callback을 실행하고, 호출하는 함수는 작업 완료 여부를 신경쓰지 않는다.
- Synchronous
  - 호출하는 함수가 호출되는 함수의 작업 완료 후 리턴을 기다리거나, 또는 호출되는 함수로부터 바로 리턴 받더라도 작업 완료 여부를 호출하는 함수 스스로 계속 확인하며 신경쓴다.

---

#### synchronous I/O 혹은 blocking I/O

- I/O 작업을 진행하는 동안 프로그램의 진행을 멈추고(block) 기다리는 방식이 사용
- 수많은 I/O 작업이 있는 경우 I/O 작업이 진행되는 동안 프로그램이 아무일도 하지 않고 시간을 소비



#### Non-Blocking algorithm

- 자원을 경쟁하는 스레드가 자원을 보호하는 상호 배제로 무기한 연기되지 않도록 하는 알고리즘
  - Wait-freedom, Lock-freedom

- 자원 사용 여부에 따라 달라짐
  - 자원 사용 가능
    - 스레드의 접근을 허용
  - 자원 사용 불가능
    - 현재 자원을 사용할 수 없다는 응답을 반환
    - 작업이 시작되었지만 아직 완료되지 않았음을 반환
  - 자원을 사용하려는 클라이언트를 등록하여 자원이 사용가능해지거나 작업이 완료될 때 클라이언트에게 알림



#### Non-blocking I/O (=Asynchronous I/O or Non-sequential I/O)

- 입출력 처리는 시작만 해둔채 완료되지 않은 상태에서 다른 처리 작업을 계속 진행할 수 있도록 멈추지 않고 입출력 처리를 기다리는 방법

- 외부에 I/O 작업을 하도록 요청한 후 즉시 다음 작업을 처리함

  - 시스템 자원을 더 효율적으로 사용할 수 있게 된다.

  - ##### 그러나 I/O 작업이 완료된 이후에 처리해야하는 후속 작업이 있을 때는 I/O 작업이 완료될 때까지 기다려야 한다.

  - 따라서 이 후속 작업이 프로세스를 멈추지 않도록 만들기 위해, I/O 작업이 완료된 이후 후속 작업을 이어서 진행할 수 있도록 별도의 약속을 한다. (Polling, Callback function)



#### Blocking vs non-blocking

- 호출되는 함수가 바로 리턴하느냐 마느냐가 관심사
- non-blocking
  - 호출된 함수가 바로 리턴해서 호출한 함수에게 제어권을 넘겨주고, 호출한 함수가 다른 일을 할 수 있는 기회를 줄 수 있다.
- blocking
  - 호출된 함수가 자신의 작업을 모두 마칠 때까지 호출한 함수에게 제어권을 넘겨주지 않고 대기하게 만든다.

---

#### Synchronous blocking I/O

- 가장 기본적인 I/O 모델로, 파일을 읽고 쓰는 일반적인 read, wrtie와 같은 I/O 시스템 호출
- application이 read()를 호출한 후 커널은 데이터가 사용자 버퍼에 복사되기 전까지 리턴하지 않는다.
  - 유저 프로세스는 자신의 작업을 중단한 채 대기 (CPU 등의 자원 낭비)
  - 한 작업당 한번의 application-kernel 공간의 문맥 교환 발생
- 상황 : 여러 클라이언트가 접속하는 서버 구현
  - 하나의 클라이언트가 I/O 작업을 진행하면 해당 프로세스 또는 쓰레드가 진행하는 작업을 중단
  - 클라이언트별로 프로세스 또는 쓰레드를 만들어 연결시켜야 한다.
  - 접속자 수가 증가하면 프로세스 또는 쓰레드가 급증한다.



#### Synchronous non-blocking I/O

- Synchronous blocking의 개선안이지만 더 잦은 시스템 호출과 문맥교환으로 더 비효율적이다.
- system call 호출하면 kernel에서 바로 리턴된다.
  - 커널 작업이 완료되지 않았다면 에러 코드를 반환 (EAGAIN or EWOULDBLOCK)
- I/O가 완료될 때까지 지속적으로 커널에게 system call 호출
- 상황 : 여러 클라이언트가 접속하는 서버 구현
  - 하나의 클라이언트가 I/O 작업을 진행하면 kernel로 부터 바로 리턴 값을 받는다.
  - 하지만 반환 값으로 에러코드를 받았다면 application이 지속적으로 read()를 호출하여 I/O 체크
  - system call이 반복적으로 일어나기 때문에 cpu 등 자원이 낭비



#### ASynchronous blocking I/O

- select나 poll 같은 시스템 호출을 이용하여 I/O 다중화를 하기 위한 목적으로 사용

- system call인 select를 호출하게 되면 application은 block 된다.
- poll은 접속자의 수가 증가하면 성능이 떨어진다.



#### ASynchronous non-blocking I/O

- application에서 system call을 하면 파일 디스크립터와 버퍼의 포인터, 버퍼의 크기 및 file offset, kernel 작업 완료시 통지 방법 등을 커널에 전달하고 커널은 즉시 리턴한다.
- 데이터가 사용자 버퍼에 복사되어 I/O 준비가 완료되면 시그널 또는 callback을 발생하여 완료를 알려준다.

---

#### Refference

-  https://www.reactivemanifesto.org/ko 
-  https://atin.tistory.com/574 
-   https://tech.peoplefund.co.kr/2017/08/02/non-blocking-asynchronous-concurrency.html 
-   https://homoefficio.github.io/2017/02/19/Blocking-NonBlocking-Synchronous-Asynchronous/ 
-   https://ozt88.tistory.com/21 
-   https://www.slipp.net/questions/367 

