## [React with TypeScript](https://www.inflearn.com/course/react-with-typescript/dashboard)
### <[실습 환경](https://ko.reactjs.org/docs/static-type-checking.html#using-typescript-with-create-react-app)>
``npx create-react-app react-typescript --template typescript``   

#### 기본 설정 파일 ([참고](https://github.com/Microsoft/TypeScript-React-Starter#typescript-react-starter))
```
react-typescript/
├─ .gitignore
├─ node_modules/
├─ public/
├─ src/
│  └─ App.css
│  └─ App.test.tsx
│  └─ App.tsx
│  └─ index.css
│  └─ index.tsx
│  └─ ...
├─ package.json
└─ tsconfig.json
```
- ``public`` contains static assets like the HTML page we're planning to deploy to, or images. You can delete any file in this folder apart from index.html.
- ``src`` contains our TypeScript and CSS code. ``index.tsx`` is the entry-point for our file, and is mandatory.
  - ``App.css`` : App Component에서 쓰이는 스타일 
  - ``App.test.tsx`` : App Component에 대한 테스트 파일
  - ``App.tsx``
    - App Component (샘플)
    - Component 이름과 파일 이름 맞추는게 관례
    - export는 항상 default로
  - ``index.css`` : 글로벌 스타일 작성
  - ``index.tsx``
    - entry file
    - ReactDOM.render()를 통해 index.html의 root(DOM)에 react Component를 매칭 해줌
    - pwa를 위한 서비스 워커 등록
- ``tsconfig.json`` contains TypeScript-specific options for our project. ([참고](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html))
- ``package.json`` contains our dependencies, as well as some shortcuts for commands we'd like to run for testing, previewing, and deploying our app.


#### 기본 커맨드
- ``npm start`` : Starts the development server.
- ``npm run build`` : Bundles the app into static files for production.
- ``npm test`` : Starts the test runner.
- ``npm run eject`` : Removes this tool and copies build dependencies, configuration files and scripts into the app directory. If you do this, you can’t go back!
