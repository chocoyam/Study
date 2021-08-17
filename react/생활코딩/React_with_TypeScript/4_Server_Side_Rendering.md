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

### 2. ReactDOMServer 맛보기
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

### 3.





