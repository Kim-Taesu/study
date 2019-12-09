#### 전역 객체

- 함수 안과 밖에서 모두 사용할 수 있도록 범위를 제한하지 않는 객체
- `console` : 콘솔 창에 결과를 보여주는 객체
- `process` : 프로세스의 실행에 대한 정보를 다루는 객체
- `exports` : 모듈을 다루는 객체

---

#### 전역변수

- `__filename` : 실행한 파일의 이름을 출력
  - 파일의 전체 패스가 출력
- `__dirname` : 실행한 파일이 들어 있는 폴더를 출력
  - 폴더의 전체 패스가 출력

---

#### console 객체

- `log()` : 콘솔에 메시지 출력
- `dir(object)` : 자바스크립트 객체의 속성들을 출력
- `time(id)` : 실행 시간을 측정하기 위한 싲가 시간 기록
- `timeEnd(id)` : 실행 시간을 측정하기 위한 끝 시간을 기록

---

#### process 객체

- `argv` : 프로세스를 실행할 때 전달되는 파라미터 정보
- `env` : 환경 변수 정보
- `exit()` : 프로세스를 끝내는 메소드

---

#### 모듈

- 별도의 파일로 분리된 독립 기능의 모음
- 모듈을 만들어 놓으면 다른 파일에서 모듈을 불러와 사용할 수 있다.
- 모듈을 사용하기 위해 `exports` 전역 객체를 사용
- 모듈을 불러올 때는 `require()` 메소드 사용
  - 모듈 객체가 반환

---

#### OS 모듈

- `hostname()` : 운영체제의 호스트 이름을 알려 준다.
- `totalmem()` : 시스템의 전체 메모리 용량을 알려준다.
- `freemem()` : 시스템에서 사용 가능한 메모리 용량을 알려 준다.
- `cpus()` : CPU 정보를 알려준다.
- `networkInterfaces()` : 네트워크 인터페이스 정보를 담은 배열 객체를 반환

---

#### path 모듈

- `join()` : 여러 개의 이름을 모두 합쳐 하나의 파일 패스로 만들어 준다.
- `dirname()` : 파일 패스에스 디렉터리 이름을 반환
- `basename()` : 파일 패스에서 파일의 확장자를 제외한 이름을 반환
- `extname()` : 파일 패스에서 파일의 확장자를 반환

---

#### forEach()

- ```javascript
  object.forEach(function(item, index)){
  	console.log(index,item)	;
  };
  ```

---

#### 배열 메소드

- `push(object)` : 배열의 끝에 요소를 추가
- `pop()` : 배열의 끝에 있는 요소를 삭제
- `unshift()` : 배열의 앞에 요소를 추가
- `shift()` : 배열의 앞에 있는 요소를 삭제
- `splice(index, removeCount, [Object])` : 여러 개의 객체를 요소로 추가하거나 삭제
- `slice(index, copyCount)` : 여러 개의 요소를 잘라내어 새로운 배열 객체로 만든다.

---

#### 프로토타입 객체

- 자바스크립트에는 클래스라는 개념이 없고, `프로토타입`이라는 개념이 존재
- `객체 지향`언어는 `객체의 원형(prototype)`인 클래스를 만들고, 클래스에서 새로운 인스턴스 객체를 여러 개를 생성
- 프로토 타입 객체를 정의하고 나면 `new` 연산자를 사용하여 인스턴스 객체들을 만들 수 있다.
  - `new`는 생성자
- `this`는 함수를 호출한 객체
- `prototype` 속성에 데이터나 함수를 속성으로 추가하면 실제 인스턴스 객체를 만들 때 메모리를 효율적으로 관리
  - `prototype` 객체는 함수 객체를 만들 때 함수 안에 `prototype`이나 `constructor`와 같은 객체들이 자동으로 생성
  - `constructor` 객체는 생성자이며 `new` 연산자로 인스턴스  객체를 만들 때 사용

---

#### Prototype Object

- 객체는 언제나 함수로 생성된다.

  - `Prototype Object`는 함수로 생성된 객체이다.

  - ```javascript
    // 아래 코드는 같은 결과
    var obj={};
    var obj = new Object();
    ```

- 함수가 정의 될 때 2가지 일이 동시에 실행
  - 해당 함수의 `Constructor(생성자)` 자격 부여
  - 해당 함수의 `Prototype Object` 생성 및 연결

- <img src="https://miro.medium.com/max/889/1*PZe_YnLftVZwT1dNs1Iu0A.png" alt="img" style="zoom: 50%;" />



- `constructor`은 `Prototype Object`와 같이 생성되었던 함수를 가리킨다.
- `__proto__`는 `Prototype Link`이다.

---

#### Prototype Link

- `__proto__`는 해당 객체가 생성될 때 조상이었던 함수의 Prototype Object를 가리킨다.
- `__proto__` 속성을 통해 상위 프로토타입과 연결되어있는 형태를 `프로토타입 체인`이라고 한다.

---

#### URL 객체

- `require('url');`
- `parse()` : 주소 문자열을 파싱하여 URL 객체를 만든다.
- `format()` : URL 객체를 주소 문자열로 변환

---

#### 요청 파라미터 확인

- `require('querystring');`
- `parse()` : 요청 파라미터 문자열을 파싱하여 요청 파라미터 객체로 만들어 준다.
- `stringify()` : 요청 파라미터 객체를 문자열로 변환

---

#### 이벤트

- 노드는 대부분 이벤트를 기반으로 하는 비동기 방식으로 처리
- `EventEmiiter` : 이벤트를 보내고 받을 수 있게 한다.
- ![img](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQwed7nJ9zK98HfLDN5n5ny3HleTo9levubLEyaXp-BdytuWf8r&s)
- `이벤트` : 한쪽에서 다른 쪽으로 어떤 일이 발생했음을 알려주는 것
  - `이벤트`를 받기 위해 `이벤트 리스너`를 등록해야 한다.

#### 이벤트 보내고 받기

- 노드의 객체는 `EventEmitter`를 상속받을 수 있다.
  - 상속받으면 `on()`과 `emit()` 메소드를 사용할 수 있다.
  - `on()` : 이벤트가 전달될 객체에 이벤트 리스너를 설정, 리스너 함수는 객체로 전달된 이벤트를 받아 처리
  - `once()` : 이벤트 리스너를 추가하지만 한 번 실행한 후에는 자동으로 리스너가 제거
  - `emit()` : 이벤트를 다른 쪽으로 전달



---

출처

- [https://medium.com/@bluesh55/javascript-prototype-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-f8e67c286b67](https://medium.com/@bluesh55/javascript-prototype-이해하기-f8e67c286b67)