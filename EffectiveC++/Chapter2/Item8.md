## 



1) 프로그램 종료
2) 예외 처리

두 방법 모두 별로.
개발자 스스로가 예외 처리할 수 있는 방법 제공

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
