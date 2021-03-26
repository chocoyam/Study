## C++ 컴파일러가 자동으로 생성하는 것
> default constructor, destructor, copy constructor, copy assignment operator가 없는 class를 사용하면
> C++ 컴파일러가 자동으로 생성해준다
```c++
class Empty(){};

Empty e1;    //default constructor and destructor
Empty e2;    //copy constructor
e2 = e1;     //copy assignment operator

//compiler generates like below...

class Empty {
public:
  Empty() { ... }                               //default constructor
  Empty(const Empty& rhs) { ... }               //copy constructor
  
  ~Empty() { ... }                              //destructor
  
  Empty& operator=(const Empty& rhs) { ... }    //copy assignment operator
```
</br>


#### constructor
- 사용자가 constructor를 하나라도 정의하면 컴파일러는 default constructor 생성하지 않음
#### destructor
- destructor의 경우 destructor를 virtual로 선언한 base class를 상속하지 않는한 non-virtual destructor가 생성됨
#### copy constructor
- 멤버 변수가 사용자 정의 타입이면 멤버 변수의 copy constructor 호출
- 멤버 변수가 built-in 타입이면 비트를 복사
#### copy assignment operator
- 컴파일러는 합법적이고 타당하지 않으면 copy assignment operator를 생성하지 않을수도 있음
    - const나 reference 타입의 멤버변수가 있는 경우 -> 직접 연산자 구현해야 함
    - copy assignment operator가 private인 base class를 상속하는 경우
```c++
template<typename T>
class NamedObject {
public:
    NamedObject(std::string& name, const T& value) 
    :nameValue(name),
     objectValue(value)
    { 
      
    };
    
private:
    std::string& nameValue;
    const T objectValue;
};

int main()
{
    std::string newDog("Persephone");
    std::string oldDog("Satch");
    
    NamedObject<int> s(oldDog, 36);
    NamedObject<int> p(newDog, 2);
    NamedObject<int> d(s);    //copy constructor OK, s와 d의 멤버변수는 동일한 객체를 바라봄
    
    //p = s;    //compile error

    return 0;
}
```   
- //p = s; 주석 해제하면 나는 오류
![image](https://user-images.githubusercontent.com/33726146/112628272-02d91b80-8e76-11eb-87c7-6b24d067e8a0.png)

