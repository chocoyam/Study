## [React with TypeScript](https://www.inflearn.com/course/react-with-typescript/dashboard)
### 1. Server Side Rendering 하는 이유?
#### 1) SEO (Search Engine Optimization)
- 검색 엔진에서 자바스크립트를 못읽기 때문에, 크롤러에 대비해줘야 함
- 구글) 서버쪽에서도 자바스크림트로 렌더링한 후 크롤링
- 페이스북) 메타 태그 사용해서 처리
- PhantomJS 같은 도구를 이용해서 자바스크립트 렌더링한 html을 만들어 쓰기도 했는데, 요즘엔 Headless Chrome 사용
- 검색 엔진만 대응하는 서비스(prerender)
#### 2) 초기 로드 시간 단축
- 서버 리소스가 충분하다는 전제 하에

</br>

### 2. ReactDOMServer 개요
- js를 렌더링해서 HTML 스트링으로 변환해줌
#### renderToString
- data-react-checksum을 이용해서 클라이언트에서 이 html 재사용
#### renderToStaticMarkup
- 추가하는 어트리뷰트(data-react-checksum) 없이 리턴
- 용량이 적음
- 클라이언트에서 html 변경하지 않을 때
- 거의 사용 안함
#### cf. Isomorphic, Universal Rendering
- client-side, server-side 양 쪽에서 동일하게 동작하는 자바스크립트

</br>

### 3. Express로 CSR 제공하기
- Expressjs 말고 vercel(구 zeit), nginx로도 가능(?)

#### TypeScript 설정 파일 tsconfig.server.json 추가
```js
//tsconfig.server.json

{
  "compilerOptions": {
    "outDir": "build-server",
    "module": "commonjs",
    "target": "es6",
    "noImplicitAny": true,
    "sourceMap": true,
    "jsx": "react",
    "moduleResolution": "node",
    "baseUrl": "src"
  }
}
```

#### tsc 모듈 설치 및 실행 옵션
- TypeScript를 JavaScript로 변환하는 모듈   
- ``npm i typescript -g`` 명령어로 설치
- tsc -p tsconfig.server.json
  - -p 옵션
    - config 파일 경로 또는 config 파일이 위치한 폴더 경로 지정
    - default) 프로젝트 Root 폴더 밑에 있는 tsconfig.json 파일 경로
- pacakge.json 파일의 script-build 값에 "tsc -p tsconfig.server.json" 추가
  - "build": "react-scripts build && tsc -p tsconfig.server.json",
- ``npm run build`` 명령어를 통해 build 폴더 생성하고 src 안의 ts/tsx 파일 컴파일 진행
  - "* has no default export" 관련 에러 발생해서 tsconfig.server.json 파일의 compilerOptions에 ``"allowSyntheticDefaultImports": true, "esModuleInterop": true`` 추가하고 빌드함

#### Express 서버 실행 & 테스트
- express 모듈 설치
  - ``npm install express @types/express``
- src/server.ts 파일 생성
```js
import * as fs from 'fs';
import * as path from 'path';
import * as http from 'http';
import express from 'express';

const app = express();

const staticFiles = [
    '/static/*',
    '/asset-manifest.json',
    '/manifest.json',
    '/service-worker.js',
    '/favicon.ico'
];

staticFiles.forEach(file => {
    app.get(file, (req, res) => {
        const filePath = path.join(__dirname, '../build', req.url);
        res.sendFile(filePath);
    });
});

app.get('*', (req, res) => {
    const html = path.join(__dirname, '../build/index.html');
    const htmlData = fs.readFileSync(html).toString();
    res.send(htmlData);
})

const server = http.createServer(app);

server.listen(3000);
```
- tsconfig.server.json 파일 "scripts"에 "serve" 명령 추가
  - "serve": "npm run build && node build-server/server.js"
- ``npm run serve`` 명령어로 server 실행
- localhost:3000 접속
