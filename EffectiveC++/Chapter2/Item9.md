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

//derived class1
class BuyTransaction : public Transaction
{
public:
  virtual void logTransaction() const;
};

//derived class2
class SellTransaction : public Transaction
{
  virtual void logTransaction() const;
};
```
