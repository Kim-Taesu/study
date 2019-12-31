#### framework

- 필요한 기능이 대부분 만들어져 있고 추가적인 필요한 기능을 만들어 사용
- frame 밖을 벗어나기가 어렵다.

#### library

- "가져다 쓴다."

---

#### 렌더링

- DOM 요소와 스타일에 기반을 둔 레이아웃 계산
- 계산된 요소의 화면 표현

---

#### react library

- Virtual DOM 사용
  - 실제 DOM을 처리하기에는 속도가 느리다.
  - 이미 렌더링된 내용을 수정하는 것이 더 빠르다.

---

#### react 장점

- 배우기 간단하다.
- 뛰어난 Garbage Collection, 메모리 관리, 성능
- 서버 사이드 렌더링, 클라이언트 사이드 렌더링 지원
  - 유저에게 쾌적화 : 초기 렌더링을 서버에서 수행
- SEO
  - 클라이언트 렌더링만 하면 해당 사이트의 비어있는 요소만 얻는다. (데이터를 얻지 못함)
- 간편한 UI 수정 및 컴포넌트화
- 다른 프레임워크나 라이브러리와 혼용 가능



#### react 단점

- VIEW ONLY
  - 데이터 모델링, 라우팅, Ajax 기능 사용 x => 해당 기능은 라이브러리 사용

---

react.min.js : 컴포넌트를 담당

react-dom.min.js : 실제 DOM의 렌더링 하는 부분 담당

`class` : 컴포넌트 담당

- 자바스크립트 `class`는 메소드만 만들 수 있다.
- 변수를 사용할 때는 `this`를 사용
- `class`를 사용할 때 꼭 먼저 선언해야 한다.
- `static method`는 객체를 만들기 전에 사용 가능하다.
- `extends`를 통해 상속 가능
  - `super` 키워드로 부모 객체 호출 가능

---

#### JSX

- javascript 코드에서 html 형식의 코드 사용 가능
- XML-like syntax를 native JavaScript로 변경
- ES6 문법은 아님
- Nested Element
  - 모든 JSX 형태의 코드는 container element 안에 포함시켜야 한다. (`div` 등)
- JavaScript Expression
  - `{}`로 wrapping한다.
  - 변수 선언은 `let`으로 표현
    - `let`은 블록 범위에서 변수 범위를 갖는다.
    - 한번 선언하면 다시 선언할 수 없다.
  - 변수 출력은 `{변수 이름}`으로 출력
- Inline Style
  - key가 camelCase인 객체가 사용되어야 한다.
- Comments
  - `{ /*  */ }`로 사용
  - 주석은 반드시 container element 안에 작성되어야 한다.

---

#### React가 렌더링을 수행하는 시점

- 자식 컴포넌트 까지 렌더링 진행
  1. Props가 변경되었을 때
  2. State가 변경되었을 때
  3. `forceUpdate()`를 실행하였을 때

4. 부모 컴포넌트가 렌더링 되었을 때

---

#### `ReactDOM.render()` 

- 컴포넌트를 렌더링 한다.
- 실제 페이지에 JSX 형태의 코드를 렌더링할 때 사용
- 인수 
  - 1 : 렌더링할 JSX 형태의 코드 
  - 2 : 컴포넌트를 렌더링할 element

---

#### props

- 컴포넌트 내부의 Immutable Data
- JSX 내부에 {this.props.propsName}
- 컴포넌트를 사용 할 때, < > 괄호 안에 `propsName="value"`
- `this.props.children`은 기본적으로 갖고있는 `props`로서,
  -  `<Cpnt> 값 </Cpnt>`



#### 기본 값 설정

- `Component.defaultProps = { ... }`
- Component 선언이 끝난 후 `defaultProps` 객체를 선언



#### 타입 검증

- `Component.propTypes = { ... }` 설정

---

#### statue

- 유동적인 데이터
- JSX 내부에 `{ this.statue.stateName }` 사용
- 초기값 설정이 필수
  - 생성자에서 `this.state = {}` 로 설정
- 값을 수정할 때
  - `this.setState({ ... })`
  - 렌더링 이후에는 `this.state= 값` 사용 불가
- React는 값이 변경되면 자동적으로 변경

---

#### map

- 데이터 배열을 렌더링 할 때 사용
- 파라미터로 전달 된 함수를 통하여 배열 내의 각 요소를 처리해서 그 결과로 새로운 배열을 생성
- `arr.map(callback, [this.Arg])`
  - `callback` : 새로운 배열의 요소를 생성하는 함수
    - `currentValue` : 현재 처리되고 있는 요소
    - `index` : 현재 처리되고 있는 요소의 index 값
    - `array` : 메소드가 불려진 배열
  - `thisArg` : callback 함수 내부에서 사용할 this 값을 지정 (선택 사항)

---

```javascript
class Codelab extends React.Component{
  render(){
     return(
    <div>
         <h1>name is  {this.props.name}</h1>
         <h2>number is {this.props.number}</h2>
         <div>prop.children is {this.props.children}</div>
       </div>
   );
  }
}

// Codelab.propTypes = {
//   name:React.propTypes.string,
//   number: React.propTypes.number
// };

Codelab.defaultProps = {
  name:'default name',
  number: 'default number',
  children : "default children"
};

class App extends React.Component{
  render(){
    return(
      <Codelab name={this.props.name} number={this.props.number}>{this.props.children}
        </Codelab>
       
    );
  }
}


ReactDOM.render(<App name='kts' number={51}>childrean props</App>,document.getElementById('root'));
```

---

```javascript
class Counter extends React.Component{
  constructor(props){
    super(props);
    this.state={
      value:0
    };
    this.handleClick=this.handleClick.bind(this);
  }
  
  handleClick(){
    this.setState({
      value:this.state.value + 1
    });
  }
  
  render(){
    return(
      <div>
        <h2>{this.state.value}</h2>
        <button onClick={this.handleClick}>Press Me</button>
      </div>
    );
  }
}

class App extends React.Component{
  render(){
    return(
      <Counter/>
    );
  }
}

ReactDOM.render(<App/>,document.getElementById('root'));
```

---

```javascript
class ContactInfo extends React.Component{
  render(){
    return(
      <div>{this.props.contact.name}  {this.props.contact.phone}</div>
    )
  }
}

class Contact extends React.Component{
  
  constructor(props){
    super(props);
    this.state={
      contactData:[
        {name:'kttts',phone:'010-0610-0449'},
        {name:'phg',phone:'010-0610-0449'},
        {name:'jmr',phone:'010-0252-0449'},
        {name:'kek',phone:'010-2310-0449'},
        {name:'ksy',phone:'010-0610-0449'}
      ]
    }
  }
  
  render(){
    
    const mapToComponent = (data) =>{
      return data.map((contact, i) => {
        return (<ContactInfo contact={contact} key={i}/>)
      });
    };
    
    return(
      <div>
        {mapToComponent(this.state.contactData)}
      </div>
    );
  }
}

class App extends React.Component{
  render(){
    return <Contact/>
  }
}

ReactDOM.render(<App/>,document.getElementById('root'));
```

