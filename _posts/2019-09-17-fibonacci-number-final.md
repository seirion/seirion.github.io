---
layout: post
title:  "피보나치 수열 총정리"
comments: true
date:   2019-09-17 01:11:00
---


이 글은 피보나치 수열의 n 항을 구하는 여러 가지 방법에 대한 정리 글입니다.


# 1. Recursion

```kotlin
fun fibonacciRecursion(n: Int): Int {
    if (n == 0) return 0
    if (n == 1) return 1
    return fibonacciRecursion(n - 1) + fibonacciRecursion(n - 2)
}
```

많은 코딩 테스트를 해 본 결과 대부분의 사람들은 이 방식으로 코딩합니다.

하지만 이렇게 구현하면 O(2^n) 의 극악의 성능을 자랑하는데 이유는 반복 호출이 많기 때문입니다.

이렇게 코딩을 했다면 반드시 memoization 을 언급해야 옳은 답으로 평가받을 수 있습니다.

또한 n 이 매우 커지면 stack overflow 를 발생시킬 수 있으므로,

구현이 간단한 것은 장점이지만 좋은 답으로 평가하기는 어렵습니다.


# 2. 반복문

피보나치 수열의 특성을 생각하면 반복문을 이용하여 구현하는 것이 정석이라고 생각합니다.
```kotlin
fun fibonacciLoop(n: Int): Int {
    var a = 0
    var b = 1
    repeat(n) {
        b += a
        a = b - a // temp 변수를 사용하지 않으려다보니 ...
    }
    return a
}
```

딱 n 번 루프를 수행하므로 시간 복잡도는 O(n) 입니다.


# 3. Tail call Optimization
재귀로 구현하면 코드가 간단해 진다는 장점을 이용하여,

tail call 이 가능한 형태로 구현을 하는 것도 한 방법입니다.

참고: https://en.wikipedia.org/wiki/Tail_call
```kotlin
fun fibonacciTailRecursion(n: Int): Int {
    return fibonacciTailRecursion(n, 0, 1)
}

tailrec
fun fibonacciTailRecursion(n: Int, a: Int, b: Int): Int {
    if (n == 0) return a
    else return fibonacciTailRecursion(n - 1, b, a + b)
}
```

Tail call optimization 은 간단히 말해 자기 자신을 호출하면서 끝나는 메소드는

스택을 재사용할 수 있으므로 반복문으로 변경할 수 있다는 개념입니다.

C++ 이나 자바 등은 재귀 호출 최적화 옵션을 지원하므로 위와 같은 구현이 가능합니다.

코틀린의 경우 키워드(tailrec) 하나면 됩니다.


# 4. 함수형

```kotlin
fun fibonacciFunctional(n: Int): Int {
    return (0 until n)
            .fold(0 to 1) { acc, _ -> acc.second to acc.first + acc.second }
            .first
}
```
함수형 코딩 스타일로 만들어 봤습니다.

fold() 라는 메소드를 이용한 것 외엔 특별할 건 없습니다.

2개의 항을 유지해야 하므로, `pair` 형식으로 초기값을 설정하고,

메소드 `fold()` 의 두 번째 항인 operator 에 fibonacci 정의에따라 람다식을 정의합니다.

시간 복잡도는 O(n) 이지만 객체를 매번 생성하는 이유로 실제 수행시간은 매우 오래 걸립니다.


# 5. 지수 계산

피보나치 수열의 일반항 공식을 이용하는 방법입니다.

참고: https://en.wikipedia.org/wiki/Fibonacci_number

일반항은 기본적으로 지수 함수로 되어 있는데,

지수 함수를 O(log n) 에 계산할 수 있다는 것을 생각한다면,

결국 앞에서 구현한 것보다 훨씬 빠른 성능의 메소드를 구현할 수 있습니다.

아래는 행렬과 행렬의 곱셈을 이용하여 구현한 것입니다.

코드가 길지만 가장 빠릅니다.
```kotlin
fun fibonacciPower(n: Int): Int {
    if (n == 0) return 0
    if (n == 1) return 1
    var r = n

    var a = arrayOf(1, 0, 0, 1)
    var m = arrayOf(1, 1, 1, 0)
    while (0 < r) {
        if (r % 2 == 1) {
           a = multiply(a, m)
        }
        m = multiply(m, m)
        r /= 2
    }
    return a[1]
}

fun multiply(a: Array, b: Array) : Array {
    return Array(4) { 0 }
            .apply {
                this[0] = (a[0] * b[0] + a[1] * b[2]);
                this[1] = (a[0] * b[1] + a[1] * b[3]);
                this[2] = (a[2] * b[0] + a[3] * b[2]);
                this[3] = (a[2] * b[1] + a[3] * b[3]);
            }
}
```

이 구현에 대한 보다 자세한 설명은 [이곳](http://seirion.github.io/fibonacci-number/)에서 볼 수 있습니다.
