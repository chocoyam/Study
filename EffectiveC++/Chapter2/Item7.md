## base class의 destructor를 virtual로 선언하자
#### 문제 상황
- base class와 이를 상속받는 여러 derived class가 있고, base class의 포인터 타입을 리턴하는 팩토리 함수가 있는 상황
- 이 때 base class의 destructor가 non-virtual일 때 문제 발생
```c++
class TimeKeeper {
public:
  TimeKeeper();
  ~TimeKeeper();
};

class AtomicClock : public TimeKeeper { ... };
class WaterClock : public TimeKeeper { ... };
class WristWatch : public TimeKeeper { ... };
```
```c++
TimeKeeper* getTimeKeeper();  //TimeKeeper를 상속받는 derived class의 객체 반환
```
```c++
TimeKeeper* ptk = getTimeKeeper();

delete ptk;  //partially deleted 
```
- c++ 에서는 non-virtual destructor를 가진 base class의 포인터를 통해 derived class가 delete 되는 동작이 정의되어 있지 않음
- 따라서 base class에서 정의된 멤버들은 delete 되지만, derived class에만 있는 파생된 부분은 delete 되지 않음
- 즉, 객체가 부분적으로 delete됨


#### 해결 방법
- base class의 destructor를 virtual로 선언하면 derived class의 파생된 부분까지 delete 가능
```c++
class TimeKeeper {
public:
  TimeKeeper();
  virtual ~TimeKeeper();
};
```
```c++
TimeKeeper* ptk = getTimeKeeper();

delete ptk;  //all deleted 
```

- virtual 함수가 한개라도 있는 class는 반드시 virtual destructor를 가짐
- non-virtual destructor class는 base class로 사용하지 않을 것임을 나타냄
</br>

## base class가 아닌데 destructor를 virtual로 선언했을 때의 문제점
- 데이터 타입의 크기, 다른 언어와의 호환이 중요한 경우
- class에 virtual 함수가 있는 경우 class에는 vptr이라는 멤버 변수가 추가되어 class를 객체화 했을 때 객체의 크기가 커지게됨

####  vtbl(virtual table), vptr(virtual table point)
- vptr과 vtbl은 런타임에 어떤 가상 함수의 구현부를 사용할 것인지 결정하기 위한 정보
- vtbl은 가상 함수들의 포인터 배열로, class가 가상 함수를 정의할 때마다 vtbl에 해당 함수 포인터가 추가됨
- vptr은 vtbl을 가리키는 포인터
