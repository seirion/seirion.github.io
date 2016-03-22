---
layout: post
title:  "부분 집합 출력하기"
comments: true
date:   2012-12-12 11:17:00
---

집합 S = {0, 1, ..., n} 이 있을 때, 모든 부분집합 P(S) 를 출력하는 코드

가장 간단히는 비트 플래그를 이용하는 방법이 있다.

단, 집합 원소 크기가 31로 한정되는 단점이 있다 (아래 코드에서는)

아래 코드에서 MAX_VALUE 의 타입을 변경하면 64 개까지 가능하다.

```cpp
void printSubset(int n) {  
    int *list = new int [n];
  
    int i;  
    for (i = 0; i < n; i++) {  
        list[i] = i;  
    }  
  
    const int MAX_VALUE = 0x1 << n;  
    for (i = 0; i < MAX_VALUE; i++) {  
        for (int j = 0; j < n; j++) {  
            if (i & (0x1 << j)) {  
                cout << list[j] << "  ";  
            }  
        }  
        cout << endl;  
    }  
  
    delete [] list;  
}
```

다른 방법으로는 아래와 같이 재귀 호출을 통해 출력하는 방법이다.

이것은 set[] 에 원소를 추가 또는 추가하지 않는 호출을 반복하는 것이다.

```cpp
// int set[128];  
// printSubset(set, 0, n, 0);  
void printSubset(int set[], int set_size, int n, int index) {  
    if (index == n) {  
        for (int i = 0; i < set_size; i++) {  
            cout << set[i] << " ";  
        }  
        cout << endl;  
        return;  
    }  
  
    set[set_size] = index;  
    printSubset2(set, set_size + 1, n, index + 1);  
    printSubset2(set, set_size, n, index + 1);  
} 
```
