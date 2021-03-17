# #define 대신 const, enum, inline 사용하기 (Item2)
= prefer compiler to the preprocessor
```c++
#define ASPECT_RATIO 1.653

const double AspectRatio = 1.653;  //prefer this
```
- compile 전에 preprocessor가 ASPECT_RATIO를 1.653으로 코드 자체를 변경하기 때문에 ASPECT_RATIO는 symbol table에도 추가되지 않음
- 만약 ASPECT_RATIO를 사용하는 부분에서 compile 에러 나면 ASPECT_RATIO가 아닌 1.653으로 에러 발생하기 때문에 디버깅 소요 시간 증가
- 그리고 #define 매크로 사용은 const보다 코드 양이 증가할 수 있음
</br>

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
</br>

## enum
#### enum hack
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
</br>

## inline
#### macro 함수 대체
- 매크로 함수는 가독성 문제로 정의 할 때 괄호 맞추기가 힘듦
- 증가/감소 연산자를 매크로 함수의 인자로 넣을 경우 함수가 호출되기 전에 증가/감소 연산자가 수행되는 문제 발생
```c++
#define CALL_WITH_MAX(a,b) f((a) > (b) ? (a) : (b))

int a = 5, b = 0;
CALL_WITH_MAX(++a, b);  //a 두번 증가
CALL_WITH_MAX(++a, b+10); //a 한번 증가
```
- inline 함수는 위의 매크로 함수의 문제를 일으키지 않음
- 매크로 함수는 전처리기가 치환하고 inline 함수는 컴파일러가 처리
- 일반 함수 호출 시 별도의 레이블로 점프하여 실행되는 것과 달리 호출 부분을 컴파일러가 함수 전체 코드로 치환하여 컴파일함 -> 함수 호출 비용 감소   
(inline이 붙어 있더라도 성능상 이슈로 컴파일러가 인라이닝 안할 수도 있음. 반대로 inline이 붙지 않아도 성능 개선을 위해 인라이닝 하는 경우도 있음)
```c++
template<typename T>
inline void callWithMax(const T& a, const T& b)
{
  f(a > b ? a: b);
}
```
</br>
</br>

# Use const whenever possible
const는 어떤 객체가 수정되면 안된다는 의미적 제약
## 1) const 사용 범위
- class 외부 : global/namespace 범위의 상수, file/function/block 범위의 static 객체
- class 내부 : static/non-static 멤버 변수
- pointer : pointer 값(주소값)이 const인지, pointer가 가리키는 데이터가 const인지 (혹은 둘 다)

## 2) pointer에서의 const
#### const 위치에 따라 다른 의미 가짐
```c++
char greeting[] = "Hello";

char *p = greeting;  //pointer, data 둘 다 non-const
const char *p = greeting;  //data만 const (* 왼쪽에 const 위치)
char const *p = greeting;  //data만 const (* 왼쪽에 const 위치)
char * const p = greeting;  //pointer만 const (* 오른쪽에 const 위치)
const char * const p = greeting;  //둘 다 const
```

#### STL iterator


