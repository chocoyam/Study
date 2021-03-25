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


# 가능한 const 사용하기 (Item3)
> const는 어떤 객체가 수정되면 안된다는 의미적 제약   
> const로 선언하면 컴파일러가 usage error를 잡아줄 수 있음   
> 컴파일러는 bitwise constness를 준수하지만, logical constness를 사용해서 프로그래밍 해야함   
> const 멤버함수와 non-const 멤버 함수는 필연적으로 구현이 중복됨으로 non-const 함수에서 const 함수를 호출하도록 함(const 캐스팅 이용)   
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
#### const 멤버 함수와 non-const 멤버 함수의 중복 피하기
* const 멤버 함수와 non-const 멤버 함수는 내부 동작이 중복됨
* 중복 제거를 위해 non-const 멤버 함수에서 const 멤버 함수를 호출하는 방법
* casting은 지양되지만 코드 중복이 더 재앙
```c++
class TextBlock {
public:
  const char& operator[](std::size_t position) const; //
  {
    ... //범위 체크 등
    return text[position];
  }
  
  char& operator[](std::size_t position)
  {
    return const_cast<char&>(static_cast<const TextBlock&)(*this)[position]); //operation[]의 리턴타입은 const 제거,
                                                                              //*this(non-const object)는 const로 캐스팅해서 const operator[] 호출
  }
}
```
* const 객체를 non-const로 캐스팅 하는 것은 객체가 변경될 위험이 있음
* 반면에 non-const 객체는 const 관련된 위험이 없기 때문에 (객체가 수정되어도 됨으로) non-const 객체를 const_cast 캐스팅을 하는게 나음
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


# 객체 사용 전에 꼭 초기화(initialize) 하기 (Item4)
## Why?
- C++에서는 객체 초기화 안하면 자동으로 0으로 초기화가 될수도 있고 초기화가 아예 안될수도 있음
- 그리고 초기화되지 않은 데이터를 읽는 것은 undefined behavior을 발생시킴
</br>

## 초기화 규칙
- 초기화 규칙은 객체 타입마다 달라서 복잡함 그래서 항상 객체 사용 전에 초기화 하자

#### built-in 타입의 일반 변수(non-member object)는 직접 초기화
```c++ 
int x = 0;
const char* text = "A C-stype string";
double d;
std::cin >> d;
``` 

#### 클래스 안의 객체는 모두 constructor에서 초기화
- initialize != assignment
- 클래스 멤버 변수의 초기화는 constructor의 body에 들어가기 전에 수행됨
- 객체 초기화는 constructor의 initialization list를 사용
- 그런데 built-in 타입의 경우 assignment 전에 초기화 됐다는 보장은 없음(?)
```c++
class PhoneNumber {...};
class ABEntry {
public:
  ABEntry(const std::string& name, const std::string& address, const std::list<phoneNumber>& phones);
  
private:
  std::string theName;
  std::string theAddress;
  std::list<PhoneNumber> thePhones;
  int numTimesConsulted;
};

ABEntry::ABEntry(const std::string& name, const std::string& address, const std::list<phoneNumber>& phones)
: theName(name),
  theAddress(address),
  thePhones(phones),
  numTimesConsulted(0)
{
}

/* this is assignment */
//ABEntry::ABEntry(const std::string& name, const std::string& address, const std::list<phoneNumber>& phones)
//{
//  theName = name;
//  theAddress = address;
//  thePhones = phones
//  numTimesConsulted = 0;
//}
```
- constructor에서 assignment를 이용해 초기화는 하는 것은 멤버 변수들의 default constructor가 호출된 다음에 다시 assignment가 이루어지므로 효율적이지 않음 (default constructor에서 한 셋팅 작업이 무의미해짐)
- initialization list를 사용하면 각 멤버변수의 constructor에 인자를 넘겨줄 수 있음 (copy constructor를 통해)
- built-in 타입들은 initialization과 assignment 비용 차이 없지만 일관성을 위해 initialize 하자
- 멤버 변수를 default constructor로 초기화 하고 싶을때는 초기화 인자를 비워두면 됨
```c++
ABEntry::ABEntry()
:theName(), //call default constructor
theAddress(), //too
thePhones(),  //too
numTumesConsulted(0)
{
}
```
- 멤버 변수가 const나 reference인 경우 반드시 initialize 하자 (because they cant be assigned(?))

#### 초기화 순서
- base 클래스는 derived 클래스보다 먼저 초기화됨
- 클래스 안의 멤버 변수는 선언된 순서대로 초기화됨 (initialization list에 순서가 다르더라도) -> 하지만 만일의 버그를 위해 멤버 변수의 선언 순서와 초기화 순서 동일하게 하자

#### 여러 translation unit에 정의된 non-local static 객체는 초기화 순서 보장 안됨
- tranaslation unit()
    - c++ 컴파일 기본단위
    - 하나의 object 파일을 생성하는 소스코드와 (directlry or indirectly)include 된 헤더 파일로 구성
- static 객체
    - static 객체는 생성 시점부터 프로그램 종료시 까지 존재 (따라서 스택, 힙 기반 객체들은 제외)
    - 글로벌 객체, namespace 안에 정의된 객체, 클래스 내부에 static으로 정의된 객체, 함수 내부에 static으로 정의된 객체(local static 객체), non-local static 객체 등
