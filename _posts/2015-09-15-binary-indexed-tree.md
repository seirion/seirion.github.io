---
layout: post
title:  "binary indexed tree 구현"
comments: true
date:   2015-09-15 21:45:00
---

binary indexed tree 에 대한 설명 : 

[1] [https://en.wikipedia.org/wiki/Fenwick_tree](https://en.wikipedia.org/wiki/Fenwick_tree)

[2] [https://www.topcoder.com/community/data-science/data-science-tutorials/binary-indexed-%20trees/](https://www.topcoder.com/community/data-science/data-science-tutorials/binary-indexed-%20trees/)

[3] [http://blog.secmem.org/486](http://blog.secmem.org/486)

#### 트리 생성 (update)

```cpp
void update(int x, int value) {
    while (x <= n) {
        tree[x] += value;
        x += -x&x;
    }
}
```

#### 구간 합 [1, x]

```cpp
int sum_from_1_to(int x) {
    int s(0);
    while (x) {
        s += tree[x];
        x &= x - 1;
    }
    return s;
}
```

