## 소멸자에서 예외 발생시 처리 방법
> 소멸자에서 예외가 발생했을때 이 예외를 잘 처리하지 않으면,   
> 소멸자를 호출한 곳으로 예외가 전달되어 메모리 릭이나 undefined behavior을 초래하게 됨

### 예외 처리 방법
#### 1) 프로그램 종료
```c++
DBConn::~DBConn()
{
  try {db.close();}
  catch (...) {
    //make log entry that the call to close failed;
    std::abort();
  }
}
```
#### 2) 예외 무시 (swallow the exception)
```c++
DBConn::~DBConn()
{
  try {db.close();}
  catch (..._ {
    //make log entry that the call to close failed;
  }
}
```
위 두 가지 방법은 예외에 대해 대응할 방법이 없기 때문에 두 방법 모두 별로.
#### 3) 개발자 스스로가 예외 처리할 수 있는 방법 제공

```c++
class DBConn {
public:
  ...
  void close()
  {
    db.close();
    closed = true;
  }
  
  ~DBConn()
  {
    if (!closed) {
      try {
        db.close();
      } catch (...) {
        //close 실패한 log 생성 후 프로그램 종료하거나 예외 무시
      }
    }
  }
 
private:
  DBConnection db;
  bool closed;
}
```
