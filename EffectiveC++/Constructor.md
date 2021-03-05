# Constructor
## Default Constructor
- No parameter or every parameter has default value
```c++
class A {
public:
  A();  //default constructor
};

class B {
public:
  explicit explicit B(int x = 0, bool b = true);  //default constructor
}
```
<br/>

## Explicit Constructor
- 컴파일러가 묵시적(implicit) 형변환 하는 것을 방지
- 묵시적 형변환으로 인해 버그 발생할 수 있음
```c++
void doSomthing(B bObject);  //function declaration

B b1;
doSomthing(b1);
B b2(28);

doSomthing(28);  //Compile Error!

doSomthing(B(28));  //Explicit Conversion
```

<br/>

## Copy Constructor & Copy Assignment Operator
```c++
class Widget {
public:
  Widget();
  Widget(const Widget& rhs);  //Copy Constructor
  Widget& operator=(const Widget& rhs);  //Copy Assignment Operator
}
```
### Copy Constructor
#### 1. copy constructor 호출될 때 (기존 객체로 새로운 객체를 생성하는 개념)
- 기존에 생성된 객체로 동일한 타입의 객체를 생성할 때
- 어떤 class의 객체가 return될 때
- 객체가 함수의 인자로 passed by value 될때
- 컴파일러가 임시 객체 생성할 때 (not guaranteed?)
```c++
Widget w1;
Widget w2(w1);  //Copy Construct
Widget W3 = w2;

bool hasAcceptableQuality(Widget w);
if (hasAcceptableQuality(w3)) ...
```
#### 2. copy constructor를 재정의 해야할 때
- class가 file handle, network connection 등 어떤 객체의 포인터나 런타임에 할당 받는 자원을 가지고 있을때
- shallow copy(default) & depp copy   
![image](https://user-images.githubusercontent.com/33726146/110080448-d0d01e80-7dcd-11eb-8f93-b3fa4b57a68e.png)
![image](https://user-images.githubusercontent.com/33726146/110080507-e5acb200-7dcd-11eb-9f59-bb0403607c07.png)

### Copy Assignment Operator
```c++
Widget w1, w2;
w1 = w2;  //Copy Assignment Operator
```


##### Reference
- https://www.geeksforgeeks.org/copy-constructor-in-cpp/
