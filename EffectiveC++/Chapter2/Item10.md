## assignment operator의 결과로 현재 class의 reference 값을 return 하도록 하자
> built-in type과   
> vector, string, shared_ptr 등 standard library에 있는 type들은 이를 준수함

#### 바로 이렇게
```c++
class Widget {
public:
  Widget& operator=(const Widget& rhs)  //현재 class의 reference 리턴
  {
    //do something...
    return *this;
  }
  
  Widget& operator+=(const Widget& rhs)
  {
    //do something...
    return *this;
  }
  
  Widget& operator=(int rhs)
  {
    //do something...
    return *this;
  }
  
  //다른 -=, *= 등의 assignment operator들도 마찬가지로
};
```

#### 그러면 다음과 같은 assignment operator 동작이 가능
```c++
int x, y, z;
x = y = z = 15;  //chain of assignments

x = (y = (z = 15));
```

