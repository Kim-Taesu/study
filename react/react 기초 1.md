#### React?

- 페이스북에서 개발한 유저인터페이스 라이브러리
- 개발자로 하여금 재사용 가능한 UI를 생성할 수 있게 해준다.
- `Virtual DOM` 개념 사용
  - 상태의 변함에 따라 선택적으로 유저인터페이스를 렌더링



#### React 기초

- 웹 애플리케이션에서 사용하는 유저 인터페이스를 재사용 가능한 `컴포넌트`로 분리하여 작성

- 파일에서 `JSX` 를 사용하려면, 꼭 `React`를 `import`해주어야 한다.

  ```javascript
  import React, { Component } from 'react';
  ```

- `webpack`을 사용하면 파일의 확장자에 따라 다른 작업을 하게 된다.

- 브라우저 상에 react 컴포넌트를 보여주기 위해서 `ReactDOM.render` 함수를 사용

  - 1st 파라미터 : rendering할 결과물

  - 2st 파라미터 : 컴포넌트를 어떤 DOM에 그릴정 정한다.

    ```javascript
    ReactDOM.render(<App />, document.getElementById('root'));
    ```

    

---

#### 컴포넌트 생성

- 클래스
- 함수



#### 클래스 형태의 컴포넌트

- `render` 함수가 꼭 있어야 한다.
  - 내부에서는 `JSX` 를 `return` 해야한다.

---

#### 참조

- https://velopert.com/3626