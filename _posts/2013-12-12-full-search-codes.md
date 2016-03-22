---
layout: post
title:  "full search 코드"
comments: true
date:   2013-12-12 10:43:00
---

대략 문제를 풀 때, 하는 수 없이 모든 경우의 수를 뒤져야 하는 경우가 있다.
이런 경우 반복문 또는 재귀(recursion)를 통해 모든 경우의 수를 체크해야 한다.
요런 문제로써, 
줄 세우기, n 개 중 m 개 뽑기, 모든 부분 집합 찾기, 트리 탐방 .... 등의 케이스가 있다.



다음 코드는 0~n-1 사이의 숫자 중에 m 개를 고르는 모든 수를 출력하는 코드이다. (combination)
요것은 부분집합  중에 원소 개수가 m 인 것만 출력하는 것과 같은 문제이다.

```cpp
// 0~4 까지에서 3개를 뽑는 경우를 출력함
print_combination(list, 5, 3);

// list : 현재 저장된 값들
// n : input size
// remain : 추가 해야 하는 원소 개수
void print_combination(vector<int> &list, int n, int remain) {
    if (remain == 0) print(list);

    int start = 0;
    if (!list.empty()) {
        start = list.back() + 1;
    }

    for (int i = start; i < n; i++) {
        list.push_back(i);
        print_combination(list, n, remain - 1);
        list.pop_back();
    }
}

void print (const vector<int> &list) {
    vector<int>::const_iterator it = list.begin();
    for (; it != list.end(); it++) {
        printf("%d ", (*it));
    }
    printf("\n");
}
```


이번에는 0~n-1 사이의 숫자 중에 m 개를 골라서 순서를 세워 출력하는 코드이다. (순열, permutation)

```cpp
void permutation(vector<int> &list, int n, int length, int level, int flag);
void print (const vector<int> &list);


int main() {
    vector<int> list;

    // 0~4 까지 중에 3개짜리 set 을 모두 출력
    permutation(list, 5, 3, 0, 0);
    return 0;
}

void permutation(vector<int> &list, int n, int length, int level, int flag) {
    if (level == length) {
        print(list);
        return;
    }

    for (int i = 0; i < n; i++) {
        if (flag & (0x1 << i)) { // used
            continue;
        }

        list.push_back(i);
        permutation(list, n, length, level + 1, flag | 0x1 << i);
        list.pop_back();
    }
}

void print (const vector<int> &list) {
    vector<int>::const_iterator it = list.begin();
    for (; it != list.end(); it++) {
        printf("%d ", (*it));
    }
    printf("\n");
}
```



