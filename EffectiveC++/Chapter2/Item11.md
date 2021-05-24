## self assignment

#### self assignment 상황
```c++
Widget w;
w = w;
```
```c++
int i = 2;
int j = 2;
a[i] = a[j];
```
```c++
class Base { ... };
class Derived : public Base { ... };

void doSomething(const Base& rb, Derived* pd);  //인자 rb, *pd에 동일한 객체를 받을 수 있음
```
</br>

#### self assignment를 고려하지 않은 operator=
```c++
class Bitmap { ... };
class Widget
{
private:
  Widget& operator=(const Widget& rhs)
  {
    delete pBitmap;
    pb = new Bitmap(*rhs.pBitmap);
    return *this;
  }
  
private:
  Bitmap* pBitmap;
};
```
- 문제 상황 : operator= 의 연산자로 this와 동일한 Widget 객체가 들어오면, delete 연산에 의해 nullptr인 pBitmap이 셋팅됨
</br>

#### How to do?
##### 1) identity test (일치성 테스트)
```c++
Widget& Widget::operator=(const Widget& rhs)
{
  if (this == &rhs)
    return *this;
  
  delete pb;
  pb = new Bitmap(*rhs.pb);
  return *this;
}
```
- 하지만 여전히 예외 발생 가능
- new Bitmap()으로 bitmap 메모리 할당 시 메모리 부족 같은 이유로 인해 Widget의 pb는 delete 된 Bitmap을 가리키고 있을 수 있음
##### 2) 삭제->생성 순에서 생성->삭제로 순서 변경
```c++
Widget& Widget::operator=(const Widget& rhs)
{
  Bitmap* pOrigin = pb;
  pb = new Bitmap(*rhs.pb);
  delete pOrigin;
  
  return *this;
}
```

##### 3)
```c++
void Widget::swap(Widget& rhs)
{
  ...
}

Widget& Widget::operator=(const Widget& rhs)
{
  Widget temp(rhs);
  swap(temp);
  return *this;
}
```
