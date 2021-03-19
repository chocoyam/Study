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


# Use const whenever possible (Item3)
const는 어떤 객체가 수정되면 안된다는 의미적 제약
## const 사용 범위
- class 외부   
global/namespace 범위의 상수, file/function/block 범위의 static 객체
- class 내부   
static/non-static 멤버 변수
- pointer   
pointer 값(주소값)이 const인지, pointer가 가리키는 데이터가 const인지 (혹은 둘 다)
</br>


## 함수 declaration에서의 const
return 값, parameters, 함수 전체(?)에 사용
#### return 값이 const인 경우
```c++
class Rational {...};
const Rational operator*(const Rational& lhs, const Rational& rhs);

Rational a, b, c;
...
(a * b) = c;  //error, const return value can prevent this case.
...
if (a * b = c) ...  //"==" -> "=" 실수 예방가능
```
</br>


## const 멤버 함수
1) 클래스의 인터페이스를 이해하기 쉬움   
2) 클래스가 const 객체로 선언됐을 때 해당 객체와 사용할 수 있음 - const 객체는 const 멤버 함수만 사용 가능   
(performance를 위해 객체를 reference-to-const로 넘기는것이 좋기 때문에 const 객체 선언 관련 내용 중요)
```c++
class TextBlock {
public:
...
const char& operator[](std::size_t position) const { return text[position]; }  //const 객체가 사용할 const 멤버함수
char& operator[](std::size_t position) { return text[position]; }  //non-const 객체가 사용할 non-const 멤버함수

private:
  std::string text;
};
```
```c++
TextBlock tb("Hello");
std::cout << tb[0];  //non-const operator[] 함수 호출
tb[0] = 'x';  //ok

const TextBlock ctb("World");
std::cout << ctb[0];  //const operator[] 함수 호출
ctb[0] = 'x';  //error
```
```c++
void print(const TextBlock& ctb)  //const TextBlock 객체
{
  std::cout << ctb[0];  //const operator[] 함수 호출
  ...
}
```
#### 멤버 함수가 constness를 갖는다는 뜻은?   
1) bitwise constness (physical constness)   
2) logical constness   
##### bitwise constness
* 멤버 함수가 객체의 멤버 변수 값을 변경하지 않음을 뜻함 (단 static const 멤버 변수 제외)
* c++의 const 정의와 동일
* 컴파일러가 객체의 멤버 변수에 assignment가 발생하는지 bitwise test를 함
* 그런데 const 함수처럼 동작하지 않는 멤버 함수도 bitwise test를 통과함 (객체에 포인터만 존재하는 경우 멤버 함수는 bitwise constness를 갖추게 됨) 
```c++
class CTextBlock {
public:
  ...
  char& operator[](std::size_t position) const { return pText[position]; }  //멤버 변수 pText의 reference를 리턴하지만 함수 내부에서 pText를 변경하는게 아니므로 bitwise const임...
  
private:
  char* pText;
};
```
* 그런데 bitwise constness를 갖추더라도 멤버 함수 외부에서 멤버 변수를 변경할 수는 있음
```c++
const CTextBlock cctb("hello");  //const로 객체 생성
char* pc = &cctb[0];  //const operator[]로 pText 주소값 얻음
*pc = 'J';  //pText는 "Jello"가 됨
```
##### logical constness
* const 멤버 함수 내부에서 부득이하게 멤버 변수를 바꿈에도 constness를 인정
* mutable
```c++
class CTextBlock {
public:
  ...
  std::size_t length() const;
  
private:
  char* pText;
  mutable std::size_t textLength;  //mutable로 선언된 변수는 const 멤버 함수 안에서 수정 가능
  mutable bool lengthIsValid;
};

std::size CTextBlock::length() const {
  if (!lengthIsValid) {
    textLength = std::strlen(pText);  //ok
    lengthIsValid = true; //ok
  }
  return textLength;
}
```
</br>


## pointer에서의 const
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
- An iterator acts much like a T* pointer
```c++
using namespace std;

vector<int> vec;
...
const vector<int>::iterator iter = vec.begin();  //T* const
*iter = 10;
++iter; //error

vector<int>::const_iterator cIter = vec.begin();  //const T*
*cIter = 10;  //error
++cIter;
```
</br>



