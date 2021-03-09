# Undefined Behavior
https://stackoverflow.com/questions/367633/what-are-all-the-common-undefined-behaviours-that-a-c-programmer-should-know-a

<br/>
<br/>
<br/>

# Prefer consts, enums, and inlines to #define
= prefer compiler to the preprocessor

```c++
#define ASPECT_RATIO 1.653

const double AspectRatio = 1.653;  //prefer this
```

- compile 전에 preprocessor가 ASPECT_RATIO를 1.653으로 코드 자체를 변경하기 때문에 ASPECT_RATIO는 symbol table에도 추가되지 않음
- 만약 ASPECT_RATIO를 사용하는 부분에서 compile 에러 나면 ASPECT_RATIO가 아닌 1.653으로 에러 발생하기 때문에 디버깅 소요 시간 증가
- 그리고 #define 매크로 사용은 const보다 코드 양이 증가할 수 있음
