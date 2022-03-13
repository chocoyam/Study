# JQuery Intro
- https://www.w3schools.com/jquery/default.asp
## 1. JQuery란?
### > 자바스크립트 라이브러리
- 자바스크립트를 쉽고 간단하게 사용하기 위한 자바스크립트 라이브러리
### > 기능
- HTML/DOM 제어
- CSS 제어
- HTML event 제어
- Effect/Animation 제어
- AJAX 수행
- Utilities...
### > 브라우저 호환성

<br/>

## 2. JQuery 기본
#### ``$(selector).action();``
- $ : 접근자
- selector : html 요소 선택자(CSS 문법과 동일)
- action() : html 요소가 수행할 동작
- ex)   
``$(".test").hide``

#### ``$(document).ready(function() { ... });``
#### ``$(functio(){ ... });`` -> 권장
- DOM이 로드된 후 실행되는 함수
- 사용자에게 보여지기 전에 수행될 동작들을 명시
  - ex. event handler 등록, 플러그인 초기화
- 여러번 호출시 명시된 순서대로 수행됨

#### ``$("p")`` -> \<p> 태그
#### ``$("#test")`` -> id=test
#### ``$(".test")`` -> class=test
#### ``$("*")`` -> 모든 element
#### ``$(this)`` -> 현재 html element
#### ``$("p.intro")`` -> \<p> 태그 중 class=intro인 요소
#### ``$("p:first")`` -> 첫번째 \<p> 태그
[참고) jquery selector 종류](https://www.w3schools.com/jquery/trysel.asp)

#### ``$("p").on("click", function(){ ... });``
#### ``$("p").on({mouseenter: function(){ ... }, mouseleave: function(){ ... });``



