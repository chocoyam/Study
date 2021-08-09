## [React with TypeScript](https://www.inflearn.com/course/react-with-typescript/dashboard)
### 1. Component 생성
- 종류
  > - React.Component 상속
  > - React.PureComponent 상속
  > - Stateless Component(function)
  >   - Component 내부에서 state 사용 불가
  >   - Lifecycle 지원 안함

#### React.Component 상속
- props와 state의 interface 지정 해줘야 함
```js
/*
* App.tsx
*/
import React from 'react';
import './App.css';

export interface AppProps {
  name : string;
}

interface AppState {
  age : number;
}

class App extends React.Component<AppProps, AppState> {
  constructor(props : AppProps) {
    super(props);
    this.state = {
      age : 35
    };
  }
  render() {
    return (
      <div className="App">
        {this.props.name}, {this.state.age}
        <StatelessComponent name="Anna"/>  //Stateless Component
      </div>
    );
  }
}

export default App;
```
```js
/*
* index.tsx
*/
import * as React from 'react';
import * as ReactDOM from 'react-dom';
import App from './App';
import registerServiceWorker from './registerServiceWorker';
import './index.css';

ReactDOM.render(
  <App name="Mark"/>
  document.getElementById('root') as HTMLElement
);
registerServiceWorker();
```

#### React.PureComponent 상속
- props, state가 변경되면 Lifecycle 중 shouldComponentUpdate()에서 shallow compare에 따라 render() 수행 여부 결정
  - shallow compare : reference 값이 변경 됐을 경우에만 render() 수행. reference 대상의 nested object 값의 변경은 감지 못함
  - 즉 nested object 값만 변경했을 경우 reference object 전체를 copy해서 props/state로 넘겨야함
  - 번거롭기 떄문에 immutable.js라고 nested object 변경 하면 새로 reference 값만 바꿔서 주는 것을 사용
- PureComponent를 사용한다고 성능상 이점이 있는건 아님

#### Stateless Component
- React.Component, React.PureComponent 보다 속도가 빠름
```js
/*
* App.tsx
*/
...
const StatelessComponent : React.SFC<AppProps> = () => {
  return (
    <h2>{props.name}</h2>
  );
}
...
```

</br>

### 2. [Lifecycle](https://www.zerocho.com/category/React/post/579b5ec26958781500ed9955)
- Component를 class로 만들때 사용
#### 종류
- Component 생성, 마운트 관련 함수
  - ``constructor()``
  - ``componentWillMount()``[deprecated_17]
    - Component 처음 실행될 때
    - context, defaultProps, state 저장
    - 주의) props나 state 변경하면 안되고 render 전이기 때문에 DOM에 접근 불가
  - ``render()``
    - Component를 DOM에 render
  - ``componentDidMount()``
    - 마운트 완료된 시점
    - DOM 접근 가능
    - AJAX 요청, setTimeout, setInterval 같은 동작 수행
  - ``componentWillUnmount()``
    - Component 제거
- Component의 props 변경 관련 함수
  - ``componentWillReceiveProps()``[deprecated_17]
    - props 업데이트 감지 후 처음으로 호출됨
    - state 업데이트 했을때 호출되지 않음
    - 이 단계에서 setState()를 통해 state 업데이트 하면 다음 shouldComponentUpdate()에서 props, state 한번에 변경됨
  - ``shouldComponentUpdate()``
    - props, state 업데이트 시 다 호출됨 (인자로 newProps, newState 정보 받음)
    - return false하면 render 취소 (default는 true 리턴)
    - 주로 성능 최적화 작업
  - ``componentWillUpdate()``[deprecated_17]
    - 주의) 여기서 state 변경하면 props 업데이트 전에 또 shouldComponentUpdate가 발생하므로 state 변경 하지 말것
  - ``render()``
    - 변경된 props로 DOM에 업데이트 작업
  - ``componentDidUpdate()``
    - 업데이트 완료 시점에 호출
    - DOM 접근 가능
- Component의 state 변경 관련 함수
  - ``shouldComponentUpdate()``
  - ``componentWillUpdate()``
  - ``render()``
  - ``componentDidUpdate()``

</br>

### 3. Component에 Event 연결
- DOM의 event명은 JSX에서는 낙타표기법으로
  - Ex) DOM onclick => JSX onClick
- event 처리할 함수 연결
- event 처리 함수 내부에서 this를 사용한다면 scope 때문에 constructor에서 함수 binding 해줘야함 (?)
```js
/*
* App.tsx
*/
...
class App extends React.Component<AppProps, AppState> {
  constructor(props : AppProps) {
    super(props);
    this.state = {
      age : 35
    };
    this._rollback = this._rollback.bind(this);
  }
  render() {
    return (
      <div className="App">
        {this.props.name}, {this.state.age}
        <button onClick={this._rollback}>회춘</button>
        <StatelessComponent name="Anna"/>  //Stateless Component
      </div>
    );
  }
  private _rollback() : void {
    this.setState({
      age : 25
    });
  }
}

export default App;
```

</br>

### 4. Default Props
- props 값이 안들어오는 경우에 default로 설정해줄 값 지정하는 방법
#### Class Component
- class 내부에 static defaultProps 지정
```js
/*
* App.tsx
*/
...
export interface AppProps {
  name : string;
  company? : string;
}
...
class App extends React.Component<AppProps, AppState> {
  static defaultProps = {
    company : 'something'
  };
  
  constructor(props : AppProps) {
    super(props);
    this.state = {
      age : 35
    };
  }
  render() {
    return (
      <div className="App">
        {this.props.name}, {this.props.company}, {this.state.age}
        <StatelessComponent name="Anna"/>  //Stateless Component
      </div>
    );
  }
}
...
```
#### Function Component
- 해당 SFC의 defaultProps를 지정
- 방법1
```js
/*
* App.tsx
*/
...
const StatelessComponent : React.SFC<AppProps> = {props} => {
  return {
    <h2>{props.name}, [props.company}</h2>
  };
};

StatelessComponent.defaultProps = {
  company : 'Home'
};
...
```
- 방법2
```js
/*
* App.tsx
*/
...
const StatelessComponent : React.SFC<AppProps> = {name, company = "Home"} => {
  return {
    <h2>{name}, [company}</h2>
  };
};
...
```

</br>

### 5. 하위/상위 Component 변경하기
#### 계층 구조
> - Grand Parent
>   - Parent
>     - Me
>       - Child
>   - \<button></button>   
#### 하위 Component 변경
- 위와 같은 계층 구조일 때, 버튼 클릭으로 Child를 변경하려면?
  - Grand Parent에서 button에 onClick 이벤트 만들고 Grand Parent의 state 변경
  - Grand Parent에서 Parent -> Me -> Child 를 거쳐 props를 전달해야 함
#### 상위 Component 변경
- 위와 같은 계층 구조일 떄, Child에 있는 button을 클릭하여 \<p> 태그를 변경하려면?
  - Grand Parent에서 \<p> 태그에 쓰이는 state를 변경하는 함수 생성
  - Grand Parent에서 생성한 함수를 props에 넣고 Parent -> Me -> Child 로 props 전달 
  - Child에서는 button onClick 이벤트에 props로 받은 함수를 지정
#### 결론 -> Component Tree Depth가 깊어질수록 너무 복잡해짐 -> Redux, Mobx 사용하자! 
#### [ref?](https://ko.reactjs.org/docs/refs-and-the-dom.html) 하위 Component에 있는 DOM을 reference 하게 해서 상위에서 그 DOM에 작업을 할 수 있게 해줌

</br>

### 6. Composition? Inheritance?
``"Facebook은 수천개의 컴포넌트에서 React를 사용하며, 컴포넌트 상속 계층을 사용하는 것이 권장되는 use case를 찾지 못했습니다."``
``"컴포넌트에서 UI 이외의 기능을 재사용하고 싶으면, 상속을 이용하지 말고 자바스크립트 모듈로 분리해서 사용하는 것이 좋다."``

- Composition의 기본은 props의 활용
  - ex) SplitView 처럼 어떤 Component의 props에 left-component와 right-component를 지정하면, 그 Component의 왼쪽 관련 작업은 left-component가, 오른쪽 관련 작업은 right-component가 수행하도록 두는 것



