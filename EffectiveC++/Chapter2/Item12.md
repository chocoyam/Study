### Object의 모든 부분을 복사하자
- 객체지향에서 objet를 복사하는 copy function은 두 가지
  - copy constructor
  - copy assignment operator
- class 생성할 때 두 copy function을 직접 구현(오버라이딩) 하지 않으면 compiler가 자동으로 generate 해줌 (ref.Item5)

```c++
/*
* Customer.h
*/
void logCall(const std::string& funcName);

class Customer {
public:
  ...
  Customer(const Customer& rhs);
  Customer& operator=(const Customer& rhs);
  ...
  
private:
  std::string name;
  Date lastTaransaction;
```
```c++
/*
* Customer.cc
*/

Customer::Customer(const Customer& rhs)
:name(rhs.name)
{
  logCall("Customer copy constructor");
}

Customer& Customer::operator=(const Customer& rhs)
{
  logCall("Customer copy assignment operator");
  name = rhs.name;
  return *this;
}
```
