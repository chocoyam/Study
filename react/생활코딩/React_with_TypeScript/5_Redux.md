## [React with TypeScript](https://www.inflearn.com/course/react-with-typescript/dashboard)
### 1. Redux 개요
- store에 action을 보내면 store에서는 변경해야 하는 곳에 action 결과값을 전달
  - store는 한개 (Mobx는 store가 여러개)
- store 만들기
  - import redux
  - 액션 정의
  - 액션을 사용하는 리듀서 정의
  - 리듀서 합치기
  - 최종 합쳐진 리듀서를 인자로 단일 스토어 생성 - createStore
- store 사용하기
  - import react-redux
  - connect 함수를 이용해 컴포넌트에 연결

</br>

### 2. action?
- Json Object
- payload 있는 액션과 없는 액션 두 가지
  - {type: 'TEST'} //payload 없는 액션
  - {type: 'TEST', params: 'hello'} //payload 있는 액션
- type은 필수 프로퍼티이고 string 타입 (형식을 맞추지 않아도 되지만 형식을 지킴으로써 쉽게 코딩)
- 액션 생성 함수를 액션 크리에이터, 액션 생성자라고 함
  - 함수를 통해 액션을 생성해서 생성한 액션 객체 리턴
  ```js
  createTest('hello'); //{type: 'TEST', params: 'hello} 리턴
  ```
- 생성된 액션 객체는 스토어에 보내짐
#### action 준비 과정
- 액션 타입은 변수로 정의 -> 미리 정의한 변수를 사용해서 헬퍼와 오타를 줄이기 위해
  - const ADD_AGE = 'ADD_AGE';
- 액션 크리에이터 만들기
  - 액션 객체를 생성하는 함수 정의
  - 하나의 액션 크리에이터는 동일한 타입의 액션 객체만 생성
  - 액션 타입은 미리 정의한 타입으로 지정하고 사용자 생성될 액션의 타입을 지정할 수 없음
  ```js
  function addAge(age) {
    return {type: ADD_AGE, age};
  }
  ```

</br>

### 3. Reducer
- 액션을 적용한 결과를 만드는 함수
- Pure Function (?)
- Immutable -> Object Spread (?)
  ```js
  function addAgeApp(state = [], action) {
    //action 적용, 주로 switch문 사용
    switch (action.type) {
    case ADD_AGE:
      return [...state, {age: action.age, completed: false}];
    default:
      return state;
    }
    retrun state;
  }
  ```
#### Reducer 코드 관리
> 일단은 리듀서를 분할해서 만들고, 변경지점이 같은 리듀서끼리 합치는게 유지보수에 좋음
- state = {todos: [], filter: '어쩌구'} 라고 할때,
  - todos만 변경하는 액션을 처리하는 리듀서 A, filter만 변경하는 액션을 처리하는 리듀서 B를 각각 만들고
    ```js
    //todo reducer
    function todos(state = [], action) {
      switch (action.type) {
      case ADD_TODO :
        retrun ...
      case COMPLETE_TODO :
        return ...
      default :
        return ...
      }
    }

    //visivility reducer
    function visibilityFilter(state = SHOW_ALL, action) {
      switch (action.type) {
        case SET_VISIBILITY_FILTER :
          return ...
        default :
          return ...
      }
    }
    ```
  - A와 B를 합침
    - 수작업으로 합치기
      ```js
      function todoApp(state = {}, action) {
        return {
          visibilityFilter: visibilityFilter(state.visibilityFilter, action),
          todos: todos(state.todos, action)
        };
      }
      ```
    - combineReducers() 함수 사용
      ```js
      import { combineReducers } from 'redux';
      
      const todoApp = combineReducers({
        visibilityFilters,
        todos
      });
      ```

</br>

### 4. Store
> createStore(Reducer, initialValue);
#### createStore
  -  Definition
    - \<S>(reducer: Reducer\<S>, preloadedState: S, enhancer?: StoreEnhancer\<S>): Store\<S>;
#### store
  - Store\<S> Definition
  - \<S> : state의 Generic
  ``store.getState();`` -> 현재 상태의 state 반환
  ``store.dispatch(action);`` -> action 보내는것
  ``store.subscribe(listener);`` -> dispatch()를 통해 변경이 일어나면 store가 subscribe()를 호출해서 listener 함수를 실행
  ``store.replaceReducer(anotherReducer);``

</br>

### 5. Summary
1. 액션 타입 만들기
2. 액션 생성 함수 만들기
3. 리듀서 만들기
4. 스토어만들기
5. createStore위해 redux, @types/redux 추가

</br>

### 6. Redux 안쓰기 vs Redux 쓰기






