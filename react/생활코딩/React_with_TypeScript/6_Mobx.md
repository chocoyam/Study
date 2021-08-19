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
