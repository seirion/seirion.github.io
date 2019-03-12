---
layout: post
title:  "피보나치 수열 일반항 계산"
comments: true
date:   2019-03-12 23:11:00
---


병아리 시절에 [이런 댕청한 글](https://seirion.github.io/fibonacci-number-time-complexity/)을 포스트한 적이 있는데,
반성하는 마음으로 다시 피보나치 수열에 대한 글을 써 본다.

피보나치 수열의 일반항은 [[1]](http://en.wikipedia.org/wiki/Fibonacci_number) 에서 보는 바와 같이 몹시 괴랄하다

![피보나치 수열 일반항](https://wikimedia.org/api/rest_v1/media/math/render/svg/bf676e167e853211636ae5862890a08ae78cb10a)

지수 모양을 하고 있기 때문에 일반항은 O(log(n)) 시간에 구할 수 있는데,
보통 인터뷰 등에서 물어볼 때는 O(n) 시간으로 해결하는 정도로 통과가 가능하다.

단순 for 루프나 재귀 정도로 구할 수 있는 건 매우 흔하므로 넘어가고
행렬 계산을 이용하여 빠르게 해답을 구할 수 있는 방법을 써 본다.

![대략 행렬로 표현](https://wikimedia.org/api/rest_v1/media/math/render/svg/c795926ab8f80e6207800de0451de69a69b8f641)

피보나치 수열의 특성을 이용하여 위와 같이 행렬 산수를 적용해 볼 수 있다.
n 항을 알고 있는 경우 n+1 또는 n*2 항은 한 번의 행렬 연산으로 구할 수 있으니
결국 n 항을 구하는 건 log(n) 정도로 행렬 곱을 산수하면 계산할 수 있다.

```
typedef vector<long long> matrix;
matrix operator * (const matrix &a, const matrix &b) {
    matrix c(4);
    c[0] = (a[0] * b[0] + a[1] * b[2]);
    c[1] = (a[0] * b[1] + a[1] * b[3]);
    c[2] = (a[2] * b[0] + a[3] * b[2]);
    c[3] = (a[2] * b[1] + a[3] * b[3]);
    return c;
}

int fibonacci(int n) {
    if (n <= 1) return n;

    matrix r = {1, 0, 0, 1}; // 초기 값
    matrix m = {1, 1, 1, 0};

    while (0 < n) {
        if (n & 1) {
            r = r * m;
        }
        m = m * m;
        n >>= 1;
    }

    return r[1];
}
```

주의 : 피보나치 수열의 값은 지수로 증가하므로 n 이 커지면 금방 오버플로우가 난다.


references :
[1] [http://en.wikipedia.org/wiki/Fibonacci_number](http://en.wikipedia.org/wiki/Fibonacci_number)
