## [React with TypeScript](https://www.inflearn.com/course/react-with-typescript/dashboard)
### 1. React Router 개요 ([공홈](https://reactrouter.com/web/guides/quick-start/1st-example-basic-routing))
- React는 client-side-rendering을 하기 때문에 Router가 필요
- URL에 맞게 데이터를 패치하고 렌더링 하는것
- 즉 요청한 URL에 맞는 React Component를 렌더링 해서 보여주는 역할이 React-Router
- React-Router v4 기준

#### 설치 및 셋팅
- 설치   
``> npm i react-router-dom``   
``> yarn add react-router-dom``

- 셋팅
```js
//주로 사용하는 Component import
import { BrowserRouter as Router, Route, Link } from 'react-router-dom'
```

</br>

### 2. Basic Concept with Simple Coding
```js
const Home = () => {
  return (
    <h3>Home</h3>
  )
};

class App extends React.Component<{}, {}> {
  render() {
    return (
      <Router>
      <div className="App">
        <h2>I always do rendering</h2>
        <Route exact={true} path="/" component={Home}/>
        <Route path="/intro" render={() => <h3>소개</h3>}/>
        <nav>
          <ul>
            <li><Link to="/">Home</Link></li>
            <li><Link to="/intro">소개</Link></li>
          </ul>
        </nav>
      </div>
      </Router>
    );
  }
}
```
#### \<BrowserRouter> => \<Router>
- 하위에 하나의 컴포넌트만 가질 수 있음
- window.history.pushState()로 동작하는 라우터(?)
- 또다른 라우터 모듈로 HashRouter라는 Hash(#/)로 동작하는 라우터도 있음

#### \<Route>
- path : 경로 지정
- render, component, children : 렌더링 방식 지정
- exact : 실제 경로와 정확히 매칭될 때만 렌더링 할건지 설정
- match, history, location?

#### cf... \<Link>
- \<a>로 렌더링 되지만 실제 동작은 페이지 전체를 리로드하는 \<a>와 다르게 페이지에서 필요한 부분만 리로드

</br>

### 3. Router Props
#### URL Parageter Routing
``Route path="/post/:postId" component={Post}/>``   
```js
const Post = () => {
  return (
    <h3>Post</h3>
  );
}
```

- Route path에 :postId 파라미터 선언
```js
...
<Route path="/posts/:postId" component={Post}/>
...
```



