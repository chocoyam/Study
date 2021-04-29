## constructor나 destructor에서 virtual 함수 호출하지 말것

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

#### derived class 타입으로 객체 생성 시
```c++
BuyTransaction b;
```
- BuyTransaction 보다 Transaction 클래스의 생성자가 먼저 호출됨
- Transaction의 생성자 호출 시점에는 BuyTransaction의 멤버변수들이 초기화되지 않음
- 따라서 Transaction 생성자에서 호출하는 logTransaction() 함수는 Transaction 클래스거를 호출
- 즉, derived 객체로 생성됐지만, Transaction 생성자 수행 시점에서는 base 객체처럼 동작
