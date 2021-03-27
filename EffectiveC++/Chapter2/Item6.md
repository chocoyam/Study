## 컴파일러가 생성하는 함수를 사용하지 않도록 하는법

- 컴파일러가 생성하는 함수는 모두 public
- 따라서 컴파일러가 함수를 생성하지 않도록 하고, 다른 사람이 그 함수를 사용하지 못하도록 하기 위해 private으로 선언
- 단, 함수를 구현할 경우 friend 함수에서 호출할 수 있기 때문에 구현하지 말것
- private으로 선언되고 구현은 안된 함수를 friend 함수에서 호출할 경우 링킹 에러 발생

```c++
class HomeForSale {
public:

private:
  HomeForSale(const HomeForSale&);              //prevent copy constructor
  HomeForSale& operator=(const HomeForSale&);   //prevent copy assign constructor
}
```


#### 링킹 에러를 컴파일 타임에 발생시키는 법
- 생성을 막고 싶은 함수들의 접근 지정자가 private으로 된 base 클래스를 private 상속
- 컴파일러가 생성한 함수에서는 base 클래스에 있는 동일한 함수를 호출하려 하기 때문에 컴파일 에러 발생
- boost에 상속해서 쓸 수 있는 noncopyable 클래스도 있음
```c++
class Uncopyable {
protected:
  Uncopyable() {}
  ~Uncopyable() {}
  
private:
  Uncopyable(const Uncopyable&);
  Uncopyable& operator=(const Uncopyable&);
}

class HomeForSale : private Uncopyable {
  ...
};
```
