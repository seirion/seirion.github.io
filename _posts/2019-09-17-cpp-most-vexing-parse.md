---
layout: post
title:  "most vexing parse"
comments: true
date:   2019-09-28 22:11:00
---


# C++ Most Vexing Parse

간단히 설명하면, 문법의 모호함으로 인해 겪을 수 있는 문제로,

개발자는 인스턴스 생성을 의도하지만 컴파일러는 그것을 함수 선언으로 처리하는 문제이다.


```cpp
class A {
    public:
        void doSomething() {}
};

int main() {
    A a();
    a.doSomething();
    return 0;
}
```

`main` 함수의 첫 줄은 `A` 의 인스턴스를 선언하는 것으로 보이지만 실제로는 

A 객체를 반환하는 함수를 선언한 것이다.

그러므로 gcc 로 컴파일 해 보면 다음과 같은 에러를 보게 된다.

```
t.cpp: In function 'int main()':
t.cpp:8:7: error: request for member 'doSomething' in 'a', which is of non-class type 'A()'
     a.doSomething();
       ^~~~~~~~~~~
```

Clang++ 은 똑똑해서 정확한 에러 메세지를 보여준다.
```
t.cpp:7:8: warning: empty parentheses interpreted as a function declaration
      [-Wvexing-parse]
    A a();
       ^~
t.cpp:7:8: note: remove parentheses to declare a variable
    A a();
       ^~
t.cpp:8:5: error: base of member reference is a function; perhaps you meant to call it
      with no arguments?
    a.doSomething();
    ^
     ()
1 warning and 1 error generated.
```

여기서 포인트는 문제를 해결하는 방법이 아니라, 이 컴파일 에러가 vexing parse 문제라는 점을 알라차리는 것이다.

위 예제와 같이 간단한 기본 생성자 선언에서는 간단히 괄호를 없애기만 해도 문제가 없다.

`A a;`

하지만, 다음과 같은 조금 더 복잡한 경우는 훨씬 더 문제를 바로 알아보기 힘들다.

```cpp
class Timer {
 public:
  Timer();
};

class TimeKeeper {
 public:
  TimeKeeper(const Timer& t);

  int get_time();
};

int main() {
  TimeKeeper time_keeper(Timer());
  return time_keeper.get_time();
}
```
[wiki](https://en.wikipedia.org/wiki/Most_vexing_parse) 에서 발췌


해결책은 위키를 참조

