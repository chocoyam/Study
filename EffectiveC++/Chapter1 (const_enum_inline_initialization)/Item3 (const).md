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
