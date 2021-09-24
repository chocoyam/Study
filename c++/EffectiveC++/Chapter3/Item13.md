## Resource 관리하는 Object 사용하기

```C++
//root class
class Investment { ... };

//factory function
Investment* createInvestment();
```
- `createInvestment()` 함수는 Investment 타입의 객체를 리턴하는 Factory 함수
  - 리턴된 Investment 객체는 반드시 사용 후에 delete 돼야 함
- 아래처럼 로직 flow 마지막에 객체 delete 하는 방식?
  - delete가 반드시 수행됨을 보장 못함으로 좋지 않음
```c++
void f() {
  Investment* pInv = createInvestment();
  ...
  delete pInv;
}
```
