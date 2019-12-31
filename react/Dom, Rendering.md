#### DOM

- HTML, XML 문서의 프로그래밍 인터페이스인 문서 객체 모델
- 작성한 코드가 브라우저에 의해 파싱되면 DOM이 된다.
- 브랑줘에서 지원하는 개발자 툴에서 보이는 것이 DOM 이다.



#### JavaScript와 DOM

- `Javascript` : 브라우저가 읽고 어떤 작업을 할 수 있는 언어
- `DOM` : 위 `Javascript`의 작업이 이루어지는 장소

---

#### 렌더링

- 특정 웹 페이지 접속시, 해당 페이지를 화면에 그려주는 것
- HTML, CSS, JavaScript 등 개발자가 작성한 문서를 브라우저에서 그래픽 형태로 출력하는 과정
- 대부분의 부라우저는 렌더링을 수행하는 `렌더링 엔진`을 가지고 있다.
  - 파이어폭스 : Gecko
  - 사파리 : Webkit
  - 크롬 : Blink



#### 렌더링 과정

1. 서버로부터 받은 HTML, CSS를 다운로드 받는다.

   - HTML, CSS 파일은 연산과 관리가 유리하도록 Object Model로 만든다.
   - HTML --> DOM Tree
   - CSS --> CSSOM Tree

2. `DOM Tree`와 `CSSOM Tree`를 이용하여 `Render Tree`를 생성한다.

   - `Render Tree`에는 스타일 정보가 설정되어 있다.
   - 실제 화면에 표현되는 노드들로만 구성

3. Layout

   - ViewPort : 그래픽이 표시되는 브라우저의 영역, 크기
   - 브라우저의 ViewPort 내에서 각 노드들의 정확한 위치와 크기를 계산

4. Paint

   - 각 요소들을 `Layout` 과정에서 도출된 결과를 토대로 실제 화면에 그린다.

   

#### Reflow (Layout)

- 어떠한 액션이나 이벤트에 따라 HTML 요소의 크기 위치와 같은 레이아웃 수치를 수정하면 해당 노드의 자식 노드나 부모 노드들은 `Layout` 과정을 다시 수행한다.

- 바뀐 값을 토대로 각 요소들의 크기와 위치를 다시 계산

  

#### Repaint (Paint)

- `Reflow` 만 수행되면 실제 화면에 반영되지 않는다.
- 렌더링 과정과 같이 Render Tree를 다시 화면에 그려주는 과정이 필요.
  - 레이아웃에 영향을 주지 않는 스타일 속성이 변경되면 `Reflow`를 수행할 필요가 없다.



#### 렌더링 최적화

- Reflow (Layout), Repaint (Paint) 줄이기
- 사용하지 않는 노드는 `display:none`을 사용
- Reflow, Repaint가 발생하는 속성 사용 피하기



---

#### Virtual DOM

- 가상의 DOM
- 변화가 일어나면 브라우저의 DOM에 새로운 것을 넣는것이 아닌, 자바스크립트로 이루어진 가상 DOM에 한번 렌더링을 하고, 기존의 DOM과 비교하여 변화가 필요한 곳에만 업데이트를 해준다.
- DOM 변화를 최소화 시켜준다.

---

#### 참조

- https://boxfoxs.tistory.com/408
- [https://velog.io/@godori/DOM%EC%9D%B4%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80](https://velog.io/@godori/DOM이란-무엇인가)

