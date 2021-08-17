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
#### Route path에 :postId 파라미터 선언
```js
<Route path="/post/:postId" component={Post}/>
```

#### :postId 파라미터 값을 DOM에서 가져오는 방법
- react-router-dom에서 제공하는 RouteComponentProps를 통해 접근 가능
- typescript니 RouteComponentProps에 타입 지정해주기
```js
const Post = (props: RouteComponentProps<{ postId: string }>) => {
  return (
    <h3>Post {props.match.params.postId}</h3>
  );
}
```

#### RouteComponentProps가 가진 객체
- match : path 정보와 관련된 내용 (isExact, params, path, url 등)
- location : url을 다루기 쉽게 쪼개서 가지고 있음. 브라우저의 window.location 객체와 비슷
- history : 주소를 임의로 변경하거나 되돌아갈 수 있음. 브라우저의 window.history 객체와 비슷한데 SPA 동작 방식에 맞게 페이지 일부만 렌더한다는 점이 다름
```js
//RouteComponentProps의 history 사용예제
//next 버튼 누르면 현재 postid + 1 된 주소로 이동
const Post = (props: RouteComponentProps<{ postId: string }>) => {
  function goNextPost() {
    const nextPostId = +props.match.params.postId + 1;
    props.history.push(`/posts/${nextPostId}`);
  }

  return (
    <div>
      <h3>Post {props.match.params.postId}</h3>
      <button onClick={goNextPost}>next</button>
    </div>
  );
}
```

#### Query String 파싱하기
- Query String은 url에 ?key=value 포맷의 String
- DOM 단에서는 RouteComponentProps의 location.search 객체를 통해 Query String 가져올 수 있음
- search 객체는 파싱되지 않은 상태기 때문에 URLSearchParams 객체를 사용해 파싱된 값 가져올 수 있음 (URLSearchParams은 브라우저 지원율이 낮아서 다른 Query String 관련 오픈소스를 많이 씀)
```js
const Post = (props: RouteComponentProps<{ postId: string }>) => {
  function goNextPost() {
    const nextPostId = +props.match.params.postId + 1;
    props.history.push(`/posts/${nextPostId}`);
  }

  return (
    <div>
      <h3>Post {props.match.params.postId}</h3>
      <p>{new URLSearchParams(props.location.search).get('body')}</p>
      <button onClick={goNextPost}>next</button>
    </div>
  );
}
```

</br>

### 4. 중첩 Routing
- Routing을 여러 컴포넌트에 걸쳐서 단계별로 정의하는 기법
- Routing을 최상위 컴포넌트에 한번에 다 정의하게 되면 유지보수가 어려움
- 특정 url의 하위로 갈라질 수 있는 경로는 해당 url을 처리하는 Component에서 하위 경로의 Route를 명시
```js
...
<Route path="/posts" component={PostList}/>
...
const PostList = (props: RouteComponentProps<{ postId: string }>) => {
  return (
  <div>
    <Route exact = {true} path = {props.match.url} render = {() => <h3>PostList</h3>}></Route>
    <Route path = {`${props.match.url}/:postId`} component = {Post}></Route>
  </div>
  )
}
```

</br>

### 5. Switch
- \<Route> 모듈의 exact 속성과 관련
- \<Route>를 \<Switch> 태그로 감싸서 사용
- \<Route> 중 위에서부터 첫번째로 매치되는 것만 Render되므로 순서에 유의
- \<Switch> 내부에 path 속성이 지정되지 않은 \<Route>의 경우, 매치되는 \<Route> 컴포넌트가 없을 때 동작함 (default 기능)
```js
...
<Switch>
  <Route exact={true} path="/" component={Home}/>
  <Route path="/intro" render={() => <h3>소개</h3>}/>
  <Route path="/posts" component={PostList}/>
  <Route component = {NotFound}/>
</Switch>
...
const NotFound = () => {
  return (
    <h3>Not Found</h3>
  );
}
```

</br>
  
### 6. \<Redirect>
- 마운트 되면 Redirect에 지정한 경로로 이동
- 현재 주소를 교환하는 replace 방식 (history에 남지 않음)
- location 객체를 통해 Redirect 할 수도 있음

```js
...
<Route path="/admin" component={Admin}/>
...
const Admin = () => {
  const isAdmin = false; //임의로 설정
  return isAdmin ? <h3>Admin</h3> : <Redirect to="/"/>;
}
```
  
#### old url을 new url로 redirect 하고 싶을 때
- from 속성에 old url, to 속성에 new url 지정
- from은 Switch와 함께 있을 때만 사용 가능
```js
<Switch>
  <Route exact={true} path="/" component={Home}/>
  <Route path="/intro" render={() => <h3>소개</h3>}/>
  <Redirect from="/about" to="/intro"/>
  <Route path="/posts" component={PostList}/>
  <Route path="/admin" component={Admin}/>
  <Route component = {NotFound}/>
</Switch>
```
  
</br>
  
### 7. \<NavLink>
- 특별한 \<Link>
- to 속성에 지정한 path와 url이 일치하는 경우 특별한 스타일, 클래스 적용 가능
- 네비게이션에 active 스타일을 적용하기 위한 컴포넌트
- activeStyle, activeClass 속성으로 스타일 지정 가능 (activeStyle은 css 파일 작성 없이 사용 가능)
```js
...
<nav>
  <ul>
    <li><NavLink exact={true} activeStyle={{ fontSize:24 }} to="/">Home</NavLink></li>
    <li><NavLink exact={true} activeStyle={{ fontSize:24 }} to="/intro">소개</NavLink></li>
    <li><NavLink exact={true} activeStyle={{ fontSize:24 }} to="/admin">어드민</NavLink></li>
  </ul>
</nav>
...
```
