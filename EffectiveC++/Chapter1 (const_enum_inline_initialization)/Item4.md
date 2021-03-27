# 객체 사용 전에 꼭 초기화(initialize) 하기 (Item4)
> c++ 에서는 built-in 타입 초기화를 해줄떄도 있고 아닐때도 있으니 직접 초기화 하자   
> constructor에서는 멤버 변수는 initialization list를 사용해서 초기화 하고 초기화 순서는 클래스에 선언한 멤버 변수들의 순서와 동일하게 하자   
> non-local static 객체는 local static 객체로 바꿔서 translation unit 간의 초기화 순서로 인한 문제를 피하자   
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
- problem1
    - 어떤 translation unit에서 non-local static 객체를 초기화할 때 다른 translation unit에 있는 non-local static 객체를 사용하는 경우, 각각의 translation unit에 위치하는 non-local static 객체들의 초기화순서는 정해지지 않았기 때문에 문제 발생
    - non-local static 객체의 초기화 순서를 결정하는 것은 매우 어려운 일
    ```c++
    class FileSystem {
    public:
      std::size_t numDisks() const;
    };

    extern FileSystem tfs;  //글로벌 객체(non-local static 객체)

    class Directory {
    public:
      Directory(params);
      ...
    };

    Directory::Drectory(params)
    {
      ...
      std::size_t disks = tfs.numDisks();  //non-local static 객체 사용
      ...
    }

    int main()
    {
      ...
      Directory tempDir(params);  //tempDir 생성 시점에 tfs가 초기화 됐을거란 보장 없음
      ...
    }
    ```
- solution1
    - singleton pattern 개념 적용
    - non-local static 객체를 local static 객체로 변경하고 해당 객체를 리턴하는 함수 생성
    - local static 객체는 자신을 라턴하는 함수가 처음 호출될 때 초기화
    - 해당 함수를 한번도 호출하지 않을 경우 local static 객체는 생성되지 않음
    ```c++
    FileSystem& tfs()
    {
      static FileSystem fs;
      return fs;
    }
    
    Directory::Drectory(params)
    {
      ...
      std::size_t disks = tfs().numDisks(); //tfs() 함수 호출
      ...
    }
    
    Directory& tempDir()
    {
      static Directory td(params);                                       
      return td;
    }
    ```
- problem2
    - 이렇게 static 객체를 리턴하는 함수는 멀티쓰레드 환경에서 이슈가 됨
- solution2
    - 프로그램의 single thread 시작 부분에서 이런 reference 반환하는 함수들을 모두 수동으로 호출함으로써 초기화 관련 race condition 제거
