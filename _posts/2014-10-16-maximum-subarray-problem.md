---
layout: post
title:  "최대 구간 합 문제 (maximum subarray problem)"
comments: true
date:   2014-10-16 12:36:00
---

[maximum subarray problem]()

주어진 배열에서 합이 가장 큰 구간을 찾는 문제이다.

#### 다음은 O(N^3) 방법. 전체를 순서대로 뒤지는 방법이다.

```cpp
int max_sum() {  
    int input [] = {-2, 1, -3, 4, -1, 2, 1, -5, 4};  
    size_t size = sizeof(input)/sizeof(int);  
  
    int i, j, k, sum;  
    int value = 0xFFFFFFFF;  
    for (i = 0; i < size; i++) {  
        for (j = 0; j < size; j++) {  
            sum = 0;  
            for (k = i; k <= j; k++) {  
                sum += input[k];  
            }  
            value = max(value, sum);   
        }  
    }  
    trace("%d\n", value);  
    return value;  
}  
```

#### 아래는 O(N^2) 방법.

```cpp
int max_sum() {  
    int input [] = {-2, 1, -3, 4, -1, 2, 1, -5, 4};  
    size_t size = sizeof(input)/sizeof(int);  
  
    int i, j, sum;  
    int value = 0xFFFFFFFF;  
    for (i = 0; i < size; i++) {  
        sum = 0;  
        for (j = i; j < size; j++) {  
            sum += input[j];  
            value = max(value, sum);   
        }  
    }  
    trace("%d\n", value);  
    return value;  
}
```

#### O(nlog n) 방법 : divide and conquer algorithm
```cpp
int input [] = {-2, 1, -3, 4, -1, 2, 1, -5, 4};  
size_t size = sizeof(input)/sizeof(int);  
  
trace("%d\n", max_sum(input, 0, size - 1));  
  
int max_sum(int input[], int lo, int hi) {  
    // 하나 뿐일 때  
    if (lo == hi) return input[lo];  
  
    int mid = (lo + hi) / 2;  
  
    int left = 0xFFFFFFFF, right = 0xFFFFFFFF;  
    int sum = 0;  
  
    for (int i = mid; i >= lo; i--) {  
        sum += input[i];  
        left = max(left, sum);  
    }  
  
    sum = 0;  
    for (int j = mid + 1; j <= hi; j++) {  
        sum += input[j];  
        right = max(right, sum);  
    }  
  
    int single = max(max_sum(input, lo, mid), max_sum(input, mid+1, hi));  
    return max(left + right, single);  
}
```

#### O(n) 방법 : Kadane's algorithm

```cpp
int max_sum() {  
    int input [] = {-2, 1, -3, 4, -1, 2, 1, -5, 4};  
    size_t size = sizeof(input)/sizeof(int);  
  
    int max_so_far = input[0], max_ending_here = input[0];  
    for (int i = 1; i < size; i++) {  
        if (max_ending_here < 0) {  
            max_ending_here = input[i];  
            // begin_temp = i;  
        }  
        else {  
            max_ending_here += input[i];  
        }  
  
        max_so_far = max(max_ending_here, max_so_far);  
        //if (max_ending_here > max_so_far) {  
            // begin = begin_temp;  
            // end = i;  
        //}  
    }  
  
    trace("%d\n", max_so_far);  
    return max_so_far;  
}
```

