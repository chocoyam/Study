### Object의 모든 부분을 복사하자
- 객체지향에서 objet를 복사하는 copy function은 두 가지
  - copy constructor
  - copy assignment operator
- 위 두 함수는 class 생성 시 compiler가 자동으로 generate 해줌 (ref.Item5)
- 그런데 auto-generated copy function을 직접 구현(overriding) 했는데 잘못 구현된 경우 compiler는 이를 알려주지 않음

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
```c++
/*
* PriorityCustomer.h
*/
class PriorityCustomer : public Customer {
public:
  ...
  PriorityCustomer(const PriorityCustomer& rhs);
  PriorityCustomer& operator=(const PriorityCustomer& rhs);
  ...

private:
  int priority;
}
```
```c++
/*
* PriorityCustomer.cc
*/
PriorityCustomer::PriorityCustomer(const PriorityCustomer& rhs)
: Customer(rhs),      //반드시 bass class의 copy constructor 호출
  priority(rhs.priority)
{
  logCall("PriorityCustomer copy constructor");
}

PriorityCustomer& PriorityCustomer::operator=(const PriorityCustomer& rhs)
{
  logCall("PriorityCustomer copy assignment operator");
  
  Customer::operator=(rhs);   //반드시 bass class의 assignment operator 호출
  priority = rhs.priority;
  return *this;
}
```
