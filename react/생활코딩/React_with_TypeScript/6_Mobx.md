## [React with TypeScript](https://www.inflearn.com/course/react-with-typescript/dashboard)
### 1. Mobx 주요 특징
#### 1) 데코레이터 적극 활용
- tsconfig.json에 "experimentalDecorators": true 설정
- 스토어 클래스 내부에는 @observable
- 스토어 사용하는 컴포넌트에서는 @observer
#### 2) Redux와 마찬가지로 스토어, 리액트에 필요한 부분 존재
- mobx, mobx-react
#### 3) TypeScript가 Base인 라이브러리
- package.json에 @types/mobx, @types/mobx-react 로 명시할 필요 없음   
  ``npm install mobx``   
  ``npm install mobx-react``
#### 4) Redux와 다르게 단일 스토어를 강제하지 않음
#### 5) 처음 사용이 쉽다
- Redux 처럼 action, reducer 만들지 않아도 되고 데코레이터만 붙여주면 됨 (간단)

</br>

### 2. 만들어보기
1. store 클래스 생성
2. store 클래스 내부에 감시할 state 객체 추가하고 @observable 데코레이터 붙이기
    - 리액트의 state와 다름
    - object 또는 primitive
3. state 값을 다루는 함수 생성
   - getter/setter
   - action 역할 하는 함수
    - mutable!
4. state 값을 사용할 컴포넌트 생성하고 @observer 데코레이터 붙이기
5. 컴포넌트에서 state 값을 사용하거나 값을 변경하는 함수 호출 

#### 실습 코드 (버튼 누르면 숫자가 증가해야하는데 안함;;)
```js
//store.tsx

import { observable } from 'mobx';

class Store {
    @observable
    age: number = 27;
    
    addAge = () => {
        this.age++;
    }

}

export default new Store();
```
```js
//App.tsx

import React from 'react';
import logo from './logo.svg';
import './App.css';

import { observer } from 'mobx-react';
import store from './store';

@observer 
class App extends React.Component<{}, {}> {
  render() {
    return (
      <div className="App">
        <header className="App-header">
          <p>
            {store.age}
            <button onClick={() => store.addAge()}>plus</button>
          </p>
        </header>
      </div>
    );
  }
}

export default App;
```

</br>

### 3. mobx-react-devtools
> 디버깅용 툴을 제공하는 컴포넌트
> </br>   
>  ``npm install --save-dev mobx-react-devtools``   
>  ``import DevTools from 'mobx-react-devtools;``   

#### observable
- function 형태   
  ``observable(<value>)``
  - 데코레이터 없이 사용하는 방식
  - @ 없이 함수처럼 사용해서 리턴한 객체 사용
- decorator 형태   
  ``@observable <클래스 프로퍼티>``
  - 데코레이터 사용하는 방식
  - 클래스 내부 프로퍼티 앞에 붙여서 사용
  - 한 클래스 안에 여러개 @observable 존재
  - 타입스크립트는 이 방식으로!
  
  ```js
  import { observable } from 'mobx';
  
  //function 형태
  const list = observable({1, 2, 4});
  const isLogin = observable(true);
  const age = observable({age: 35});
  
  class AgeState {
    @observable
    private _age = 35;
  }
  
  const ageState = new AgeState();
  ```
  
#### observer
- function 형태   
  ``observer(<컴포넌트>);``
  - 데코레이터 없이 사용하는 방식
  - SFC에 붙여서 사용
  - 리액트 state를 사용하지 않을거면 SFC로 해도 문제 없음
- decorator 형태   
  ``@observer <컴포넌트 클래스>``
  - 리액트의 life cycle이 아닌 mobx의 life cycle을 따름 (다음 순서로 수행)
    - componentWillReact
    - componentWillUpdate
    - componentDidUpdate
  
```js
//function 형태
const StatelessApp = observer(() => {
  function addAge(): void {
    ageState.addAge();
  }
  return (
    <div>
      {ageState.getAge()}
      <button onClick={() => addAge()}>plus</button>
    </div>
  );
});

//decorator 형태
@observer
class App extends React.Component<{}, {}> {
  constructor(props: {}) {
    super(propsP;
    this.addAge = this.addAge.bind(this);
  }
  
  render() {
    return {
      <div>
        {ageState.getAge()}
        <button onClick={() => this.addAge()}>plus</button>
      </div>
    }
  }
  
  addAge() {
    ageState.addAge();
  }
}
```

#### computed
- 특징
  - getter에만 붙일 수 있음
  - 실제 컴포넌트에서 사용하는 값의 getter에 computed를 사용하면 최소 범위로 변경할 수 있음   
   ex. 40살이 넘었을 때만 나이를 올리려고 하면 40살 이하일 때는 렌더링 불필요한 경우
  - 내부적으로 최적화 (render 최소화)
    - 계산에 사용핼 observable 값이 변경될때,
    - 다른 computed 또는 reaction에 의해 호출될 때 재실행
    - observable이 변했는데 computed가 변하지 않았을때 차이(?)
- function 형태   
  ``computed(<함수>);``
  - 데코레이터 없이 사용하는 방식
  - 별 의미 없음
- decorator 형태   
  ``@computed``
  - 클래스의 getter에

```js
//function 형태
class AgeState {
  constructor() {
    extendObservable(this, {
      _age: 35,
      age: computed(function() {
        return (this._age > 40) ? this._age : 0;
      })
    })
  }
}

//decorator 형태
class AppState {
  @observable
  private _age: number = 35;
  @computed
  get age(): number {
    return (this._age >40) ? this._age : 0;
  }
}
```

#### action
- 특징
  - state를 수정하는 함수
    - 어디에 state를 수정하는 함수가 있는지 마킹
    - untracked, transaction 및 allowStateChanges로 래핑하고 리턴
  - optional이지만 mobx에서 useStrict 모드를 쓸 경우 action을 마킹하지 않으면 런타임에러 발생
    - strict 모드 설정 방법
      - tsconfig.json에 ``"enforceActions": true``로 셋팅
  - computed의 setter는 action
```js
//store.tsx

...

class Store {
    @observable
    age: number = 27;
    
    @action
    addAge = () => {
        this.age++;
    }

}

...

```


