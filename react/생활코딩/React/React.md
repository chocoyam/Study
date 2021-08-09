## [생활코딩 - React](https://www.inflearn.com/course/react-%EC%83%9D%ED%99%9C%EC%BD%94%EB%94%A9/dashboard)
### <React 소개>
> - 복잡한 front-end를 사용자 정의 태그(Component)를 만들어주는 기술 중 하나
> - 가독성, 재사용성, 유지보수 용이성 획득

</br>

### <개발환경>
> 1. 온라인 코드 편집기
> 2. 웹사이트에 react 추가
> 3. 통합 툴체인

#### :heavy_check_mark:통합 툴체인 [create-react-app](https://github.com/facebook/create-react-app) 사용
##### 1) [npm 설치](https://nodejs.org/ko/)
- node.js로 만들어진 프로그램을 쉽게 설치할 수 있는 앱스토어 같은 존재
- 14.17.3 LTS 버전 설치함
- [npm 기본 수업](https://opentutorials.org/module/4044)
##### 2-1) npm으로 create-react-app 설치
``
npm install -g create-react-app
``
##### 2-2) npx로 create-react-app 설치 (facebook 권장)
``
npx create-react-app
``
- npx는 앱을 임시로 설치해서 한번만 실행하고 지움
- 용량 절약이 되고 항상 최신 버전을 사용할 수 있음

</br>

### <create-react-app 샘플 웹앱 실행>
##### 1) workspace
- 개발 폴더 생성 (ex. react-app)
- 생성한 폴더에서 ``create-react-app .`` 입력
##### 2) run
``
npm start
``

</br>

### <기본 디렉토리 확인>
- public
  - index.html
    - 웹앱 실행 시 브라우저 상에 보여질 html 파일
    - \<div id="root">\</div> 정의
- src
  - index.js
    - entry 파일
    - document.getElementById('root') : Component들이 하위로 들어가는 부분
    - \<App /> : react로 만든 Component 태그. App.js 파일이 구현부
  - App.js
    - \<App /> 태그가 구현되어 있는 파일
    - \<div className="App">\</div> 내부 수정을 통해 js 코딩
  - index.css
    - index.js의 css 정보
  - App.css
    - App.js에 있는 Component들의 css 정보로 App.js와 함께 로드됨

</br>

### \<Deploy>
- create-react-app 개발환경에서 실행한 app은 heavy함
- deploy를 통해 resource 경량화 하여 제공하도록 함
- 경량화 작업은 create-react-app 에서 알아서 해줌   

``
npm run build
``
- build 폴더 생성됨
- 실제 서비스 할 때는 build 안에 있는 파일 사용
- 웹서버의 document root에 build 안에 있는 파일 위치시키면 됨

#### npm에서 설치할 수 있는 간단한 웹서버 serve 사용하기
``npm install -g serve``   
``serve -s build``
- -s 옵션은 서버의 document root로 설정할 path 지정

</br>

### \<Component 만들기>
> 1. 함수형
> 2. 클래스형
#### :heavy_check_mark: 클래스형 사용
```js
class Subject extends Component {
  render() {
    return (
      <header>
        <h1>WEB</h1>
        world wide web!
      </header>
    )
  }
}

class App extends Component {
  render() {
    return (
      <div className="App">
        <Subject></Subject>
      </div>
    );
  }
}
```
- 클래스 명의 첫 글자는 반드시 대문자
- 클래스는 Component를 extends (상속) 해야 함
- 클래스 안에는 render() 멤버 함수 존재
  - render() 함수는 해당 Component를 화면에 어떻게 렌더링 할건지를 나타내는 함수로, 리턴 값은 하나의 최상위 태그로 시작하는 html 코드임
- Component 생성 하면 태그처럼 사용할 수 있음 (ex. \<App>\</App>)
- 실제 실행되는 브라우저 상에는 \<App> 태그가 App 컴포넌트의 return 값으로 변환됨
- 위의 코드는 jsx이고, 실행 시에 create-react-app이 jsx -> js 로 컨버팅 해주는 것

</br>

### \<props>
- html 태그의 attribute 값 처럼 Component에 속성 값을 넘겨 주는 방법
- Component의 사용이 더 다양해짐
- class 내부에서 this.props를 통해 속성에 접근
```js
class Subject extends Component {
  render() {
    return (
      <header>
        <h1>{this.props.title}</h1>
        {this.props.sub}
      </header>
    )
  }
}

class App extends Component {
  render() {
    return (
      <div className="App">
        <Subject title="WEB" sub="world wide web!"></Subject>
      </div>
    );
  }
}
```

</br>

### \<[React Developer Tools](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=ko)>
- 크롬 확장 프로그램
- 크롬 브라우저에서 React Component를 확인할 수 있음

</br>

### \<Component를 다른 파일로 분리하기>
```js
/**
* Subject.js
**/

import React, { Component } from 'react';

class Subject extends Component {
  render() {
    return (
      <header>
        <h1>{this.props.title}</h1>
        {this.props.sub}
      </header>
    )
  }
}

export default Subject;
```
- export 키워드를 통해 Subject.js 파일에서 어떤 것을 외부에서 사용할 수 있도록 허용할 것인지 명시

```js
/**
* App.js
**/

import React, { Component } from 'react';
import Subject from './components/Subject';
import './App.css';

class App extends Component {
  render() {
    return (
      <div className="App">
        <Subject title="WEB" sub="world wide web!"></Subject>
      </div>
    );
  }
}

export default App;
```
- 사용처에서는 사용할 Component가 명시된 js 파일을 import 하기만 하면 됨

</br>

### \<state>
- props 값에 따라서 내부 구현에 필요한 데이터
- 상위 Component(App)에서 하위 Component(Subject)로 props 값 지정해 줄때 사용
- props는 외부 데이터, state는 내부 데이터로 생각
- constructor()
  - constructor() 내부에서 this.state 값으로 props 값 셋팅
  - render() 함수보다 먼저 실행됨
```js
/**
* App.js
**/

import React, { Component } from 'react';
import Subject from './components/Subject';
import './App.css';

class App extends Component {
  constructor(props){
    super(props);
    this.state = {
      mode:'read',
      subject:{title:'WEB', sub:'world wide web!'},
      welcome:{title:'welcome', desc:'hello, react!!'},
      contents:[
        {id:1, title:'HTML', desc:'HTML is for information'}
      ]
    }
  }
  render() {
    var _title, _desc = null;
    if(this.state.mode === 'welcome'){
      _title = this.state.welcome.title;
      _desc = this.state.welcome.desc;
    } else if(this.state.mode === 'read'){
      _title = this.state.contents[0].title;
      _desc = this.state.contents[0].desc;
    }
    return (
      <div className="App">
        <Subject title={_title} sub={_desc}>
        </Subject>
      </div>
    );
  }
}

export default App;
```

</br>

### \<event>
- props, state, event가 협동하면서 역동적인 app을 만듬
- props나 state 값이 변경되면 그 Component의 render() 함수가 다시 호출되고, 그 Component 하위 Component들의 render()도 다시 호출되면서 화면이 다시 렌더링됨
- Component의 props로 함수를 지정할 수 있고, 함수 props를 받은 Component의 render() 내부에서 this.props를 통해 함수 호출 가능
- bind()
  - bind()를 통해 함수 내부에서 사용할 this의 값을 지정할 수 있음
  - render() 함수 안의 this는 Component 자신을 가리키지만, 이벤트 함수 안의 this는 아무것도 가리키지 않기 때문에 this를 바인딩 해주어야 함
  - 이 방법을 이용하면 하위 Component가 event 처리할 때 상위 Component(this)의 state를 변경할 수 있음
- setState()
  - Component의 state 값 변경할 때 사용
  - this.state를 통해 직접 셋팅하면 render() 함수 호출되지 않음
  
```js
/**
* Subject.js
**/

import React, { Component } from 'react';

class Subject extends Component {
    render() {
      return (
        <header>
          <h1><a href="/" onClick={function(e){
            e.preventDefault();
            this.props.onChangePage();
          }.bind(this)}>{this.props.title}</a></h1>
          {this.props.sub}
        </header>
      )
    }
  }

export default Subject;
```

```js
/**
* App.js
**/
import React, { Component } from 'react';
import Subject from './components/Subject';
import './App.css';

class App extends Component {
  constructor(props){
    super(props);
    this.state = {
      mode:'read',
      subject:{title:'WEB', sub:'world wide web!'},
      welcome:{title:'welcome', desc:'hello, react!!'},
      contents:[
        {id:1, title:'HTML', desc:'HTML is for information'}
      ]
    }
  }
  render() {
    var _title, _desc = null;
    if(this.state.mode === 'welcome'){
      _title = this.state.welcome.title;
      _desc = this.state.welcome.desc;
    } else if(this.state.mode === 'read'){
      _title = this.state.contents[0].title;
      _desc = this.state.contents[0].desc;
    }
    return (
      <div className="App">
        <Subject title={_title}
                 sub={_desc}
                 onChangePage={function(){
                  alert('hihihi');
                  this.setState({
                    mode:'welcome'
                  });
                 }.bind(this)}         
        >
        </Subject>
      </div>
    );
  }
}

export default App;
```

</br>

### \<create 기능>
- 



