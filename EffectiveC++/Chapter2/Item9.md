## constructor나 destructor에서 virtual 함수 호출하지 말것
> base 클래스의 constructor, destructor에서는   
> derived 클래스에서 override 한 함수를 호출하지 않기 때문
```c++
//base class
class Transaction {
public:
  Transaction();
  virtual void logTransaction() const = 0;
};

Transaction::Transaction()
{
  logTransaction(); //call virtual function in constructor
}
```
```c++
//derived class1
class BuyTransaction : public Transaction
{
public:
  virtual void logTransaction() const;
};
```
```c++
//derived class2
class SellTransaction : public Transaction
{
  virtual void logTransaction() const;
};
```
</br>

#### derived 클래스 타입으로 객체 생성 시
```c++
BuyTransaction b;
```
- BuyTransaction(derived) 보다 Transaction(base) 클래스의 생성자가 먼저 호출됨
- Transaction의 생성자 호출 시점에는 BuyTransaction의 멤버변수들이 초기화되지 않음
- 따라서 Transaction 생성자에서 호출하는 logTransaction() 함수는 Transaction 클래스거를 호출
- 즉, derived 객체로 생성됐지만, Transaction 생성자 수행 시점에서는 base 객체처럼 동작
- 위의 코드를 실행하면 compiler는 warning을, linker는 error 발생시킴
![image](https://user-images.githubusercontent.com/33726146/116532870-b6bd4300-a91b-11eb-8a6a-6434111afa05.png)

#### destructor에서 virtual 함수를 호출하는 경우는?
- BuyTransaction(derived) 클래스의 소멸자가 먼저 호출됨
- 따라서 constructor에서와 마찬가지로 Transaction(base) 클래스의 소멸자가 호출된 시점에서는 derived 클래스는 없는 상태이기 때문에 derived 클래스의 virtual 함수가 호출됨

#### virtual 함수를 non-virtual 함수로 감싸고 생성자/소멸자에서 non-virtual 함수 호출하는 경우?
```c++
class Transaction {
public:
  Transaction() { init(); }  //call to non-virtual
  virtual void logTransaction() const = 0;
  
private:
  void init() { logTransaction(); }  //calls a virtual
};
```
- compile, linking 단계에서 잡지 못하고, runtime에 다음과 같은 에러 발생으로 비정상 종료됨
![image](https://user-images.githubusercontent.com/33726146/116532777-9ab9a180-a91b-11eb-9281-2185f3881e4d.png)

#### How To Solve This Problem?
- 어떻게든 constructor와 destructor에서 virtual 함수 호출하지 않도록 하기
- Transaction 예제에서는 logTransaction() 함수를 non-virtual 함수로 선언하고 생성자에서 호출
- 이때 derived 클래스 생성자 인자로 필요한 log 정보를 넘겨 logTransaction() 함수 인자로 넣어 호출할 수 있도록 수정
- logTransaction() 함수 같은 helper 함수를 static으로 정의하면 더 안전하게 사용할 수 있음
```c++
class Transaction {
public:
  explicit Transaction(const std::string& logInfo) { logTransaction(logInfo); }
  void logTransaction(const std::string& logInfo) const;
};

class BuyTransaction : public Transaction {
public:
  BuyTransaction(parameters)
  : Transaction(createLogString(parameters)) { ... }
  
private:
  static std::string createLogString(parameters);
}
```
