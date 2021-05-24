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
##### 2) delete->new 순에서 new->delete로 순서 변경
```c++
Widget& Widget::operator=(const Widget& rhs)
{
  Bitmap* pOrigin = pb;
  pb = new Bitmap(*rhs.pb);
  delete pOrigin;
  
  return *this;
}
```
- 동일한 Widget에 대해 assignment 연산을 하게 되면 동일한 pb를 copy하게 돼서 비효율적
- 첫 번째 방법에서 사용한 identity test를 넣는 방법도 있지만, 코드 크기 증가 등의 비용이 듦
##### 3) copy and swap
```c++
void Widget::swap(Widget& rhs)
{
  ... //exchange this's pb and rhs's pb
}

Widget& Widget::operator=(const Widget& rhs)
{
  Widget temp(rhs); //copy rhs's pb
  swap(temp);
  return *this;
  //end of this function, temp is automatically deleted (because temp is function scope object)
}

Widget& Widget::operator=(Widget rhs)
{
  //rhs is already copied (because passed by value), doing copy is not necessary
  swap(rhs);  //only swap
  return *this;
}
```
