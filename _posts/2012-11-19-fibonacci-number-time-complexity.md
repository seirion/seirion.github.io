---
layout: post
title:  "피보나치 수열 일반항 구하기와 시간 복잡도"
comments: true
date:   2012-11-19 20:17:00
---

보통 피보나치 수열의 일반항은 a0 부터 차례대로 구하는 방법이 있다.

이 방법은 time complexcity O(n) 이다.

```cpp
uint64 fibonacci(int n) {
    if (n == 0) return 0;
    if (n == 1) return 1;

    uint64 sum;
    uint64 temp1 = 0;
    uint64 temp2 = 1;
    for (int i = 1; i < n; i++) {
        sum = temp1 + temp2;
        temp1 = temp2;
        temp2 = sum;
    }

    return sum;
}
```


하지만, 피보나치 수열의 일반항[1]을 이용하면 time complexity 를 O(log n) 으로 계산 할 수 있다.[2]

물론, float point 의 곱셈 계산이 필요하므로, n 이 작으면 위 방법에 비해 시간이 더 많이 소요될 수 있다.

(아마 보통은 아래 방법이 더 오래 걸릴 것이다.)

게다가 double 형 타입의 계산 오차로 인해 n 이 충분히 크다면 대략 오차가 발생한다.

(테스트 해 보니 n=75 까지 잘 계산 되고, n=76 에서 1 오차가 발생함.)

```cpp
#include <cmath>
#define ROOT_5 2.2360679774997896964091736687313
using namespace std;
uint64 fibonacci(int n) {
    double res = (pow((1+ROOT_5)/2, n) - pow((1-ROOT_5)/2, n)) / ROOT_5;
    return (uint64)(res+.5);
}
```



결론적으론 두 번째 방법을 사용하려면 float point 연산에서의 오차를 극복할 수 없고

이를 극복하려면 또다른 테크닉이 필요하다 ㅡㅡ

 

references :

[1] [http://en.wikipedia.org/wiki/Fibonacci_number](http://en.wikipedia.org/wiki/Fibonacci_number)

[2] [http://stackoverflow.com/questions/5231096/time-complexity-of-power](http://stackoverflow.com/questions/5231096/time-complexity-of-power)
