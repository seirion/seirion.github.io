---
layout: post
title:  "n 개 중 m 개 고르는 조합 (all possible combinations)"
comments: true
date:   2014-06-19 21:10:00
---

n 개 가운데 m 개를 고르는 모든 조합

```cpp
void pick(int n, vector<int> &picked, int remain) {
    if (remain == 0) {
        //do_something_with(picked);
        return;
    }

    int mini = picked.empty() ? 0 : picked.back() + 1;
    for (int i = mini; i < n; i++) {
        picked.push_back(i);
        pick(n, picked, remain - 1);
        picked.pop_back();
    }
}
```

```cpp
uint32 next(uint32 v) {
    uint32 w;
    uint32 t = v | (v - 1);
    w = (t + 1) | (((~t & -~t) - 1) >> (__builtin_ctz(v) + 1));
    return w;
}

// n <= 31
void pick(int n, int m) {
    uint32 i = (1 << m) - 1;
    uint32 maxi = (1 << n) - 1;
    while (maxi >= i) {
        //do_something_with(i);
        i = next(i);
    }
}
```

첫 번째 방법은 vector 를 이용하여 적절한 index 를 push 하는 방법이다.
일반적인 방법.

두 번째는 n 이 31 이하인 경우 사용가능하다.
bit 연산을 하므로 매우 빠르다.
대략 비교해 보면 후자가 5 ~ 7 배 빠른 것같다. (물론 경우에 따라 다르겠지만)

어차피 n 이 너무 커지면 경우의 수가 너무 많아 계산이 불가능하므로, 후자가 유용하게 사용될 수 있을 듯.



구글링 해 보니 요런 것도 있다.
[http://www.geeksforgeeks.org/print-all-possible-combinations-of-r-elements-in-a-given-array-of-size-n/](http://www.geeksforgeeks.org/print-all-possible-combinations-of-r-elements-in-a-given-array-of-size-n/)
