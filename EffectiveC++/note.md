# Undefined Behavior
https://stackoverflow.com/questions/367633/what-are-all-the-common-undefined-behaviours-that-a-c-programmer-should-know-a

<br/>
<br/>
<br/>





# Prefer consts, enums, and inlines to #define
= prefer compiler to the preprocessor
```c++
#define ASPECT_RATIO 1.653

const double AspectRatio = 1.653;  //prefer this
```
- compile 전에 preprocessor가 ASPECT_RATIO를 1.653으로 코드 자체를 변경하기 때문에 ASPECT_RATIO는 symbol table에도 추가되지 않음
- 만약 ASPECT_RATIO를 사용하는 부분에서 compile 에러 나면 ASPECT_RATIO가 아닌 1.653으로 에러 발생하기 때문에 디버깅 소요 시간 증가
- 그리고 #define 매크로 사용은 const보다 코드 양이 증가할 수 있음

## const 
### 1) const pointer
- 포인터 타입을 const로 선언할 때 포인터(주소) 값과 포인터가 가리키는 대상의 값도 const로 지정
```c++
const char* const authorName = "Chocoyam"
```
### 2) const in class (class-specific const)
- const 변수를 멤버 변수로 만들어서 scope 제한
- static 으로 선언해서 한번만 copy 되도록
```c++
class GamePlayer {
private:
  static const int NumTurns = 5;  //static const declaration
  int scores[NumTurns];  //use
  ...
};
```

## enum
### 1) enum hack
- const 보다 #define과 유사함
- enum은 #define 처럼 pointer나 reference로 접근 못하므로 (const 상수는 접근 가능) 더 강제성 있고 메모리를 사용하지 않음
- template metaprogramming의 기초적인 기술
```c++
class GamePlayer {
private:
  enum {
    NumTurns = 5  //enum hack
  };
  
  int scores[NumTurns];
  ...
}
```

## inline
### 1) macro 함수 대체
- 매크로 함수는 가독성 문제로 정의 할 때 괄호 맞추기가 힘듦
- 증가/감소 연산자를 매크로 함수의 인자로 넣을 경우 함수가 호출되기 전에 증가/감소 연산자가 수행되는 문제 발생
```c++
#define CALL_WITH_MAX(a,b) f((a) > (b) ? (a) : (b))

int a = 5, b = 0;
CALL_WITH_MAX(++a, b);  //a 두번 증가
CALL_WITH_MAX(++a, b+10); //a 한번 증가
```
