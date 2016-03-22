---
layout: post
title:  "sum of subset problem"
comments: true
date:   2014-01-04 14:18:00
---

이것은 대략 숫자의 set 에서 요놈의 부분집합의 합으로 0 을 만들 수 있는 지 여부를 체크하는 문제이다.
예를 들면, 주어진 집합 { −7, −3, −2, 5, 8} 에서 부분집합 { −3, −2, 5} 합은 0 이 될 수 있다.

대략 이 문제를 약간 변경하면, 어떤 정수 집합의 부분집합의 합이 N 이 될 수 있는 지를 확인하는 문제로 변경할 수 있다.
algospot 의 문제 WEIRD[2] 가 그 중 하나인데, 어떤 정수 n 의 부분집합의 합이 n 이 될 수 있는 지를 검사하는 문제이다.

이 문제는 NP-complete 이다.


1. 그냥 찾기
모든 부분집합에 대해서 합을 구하여 N 과 같은 지 검사하는 방법. 
이것은 대략 O(2NN) 복잡도를 갖는다.
시도해서는 안 되는 알고리즘이다 ㅡㅡ


2. branch&bound 로 풀기 [3]
모든 부분집합을 tree 를 순회하지만 promising 하지 않으면 가지치기를 통해 더이상 검사하지 않는 방법이다.
대략 입력 w[] 에 대해 첫번째 원소부터 추가 또는 추가하지 않는 경우를 재귀(recursive)적으로 함수를 호출한다.

```cpp
// int w[] = {...}; // input set
// int x[] = {0,};
// sumOfSubsets(0, W, sum(w));

bool sumOfSubsets(int i,int weight, int total) {
    if (!promising(i)) return;
    if (weight == W) {
        //cout << x[1..n];
       return true;
    }
    else {
        //x[i+1] = 1;
        if (!sumOfSubsets(i+1, weight+w[i+1], total-w[i+1])) {
            //x[i+1] = 0;
            return sumOfSubsets(i+1, weight, total-w[i+1]);
        }
    }
}

bool promising(int i) {
    return (weight + total >= W) && (weight == W || weight + w[i+1] <= W);
}
```


이렇게 하면 promising() 계산에 시간이 다소 오래 걸린다.
90 개짜리 입력을 넣으면 수분 이상 걸린다.

거의 비슷하지만 방법을 바꾸어서, 계산을 단순화 하면 훨씬 빨라진다.
원리는 i 번째 원소가 속할 지, 속할 지 않을 지에 따라 
속할 때는 sum 에서 값을 제외해 나가는 것이다. 이렇게 하면 promising() 에 해당하는 코드가 짧아진다.

예를 들어, 입력이 {1,2,3,4,6} 이고, N = 12 일때, 
정답 sub set 에 6 이 포함된다면, 원소 6을 제외한 {1,2,3,4}, N=6 으로 문제가 간소화 된다.
만약 6이 포함 안 된다면 {1,2,3,4}, N=12 가 된다.
남아 있는 원소의 합과 변경된 N 값을 바로 계산하여 호출하면 된다.
이렇게 하면 90개짜리 입력을 넣으면 약 150 ms 내에 결과가 나온다.

```cpp
// int w[] = {...}; // input set
// int x[] = {0,};
// sumOfSubsets(size-1, sumOf(w), N);
bool sumOfSubsets(int index, int sum, int N) {
    if (sum == N) return true;
    if (sum < N || N < 0) return false;

    //x[i+1] = 1;
    if (sumOfSubsets(index-1, sum-w[index], N-w[index])) {
        return true;
    }
    //x[i+1] = 0;
    return (sumOfSubsets(index-1, sum-w[index], N));
}
```

<br /><br /><br />
references:

[1] [http://en.wikipedia.org/wiki/Subset_sum_problem](http://en.wikipedia.org/wiki/Subset_sum_problem)

[2] [http://algospot.com/judge/problem/read/WEIRD](http://algospot.com/judge/problem/read/WEIRD)

[3] [http://blog.naver.com/PostView.nhn?blogId=takku04&logNo=150017480080&redirect=Dlog&widgetTypeCall=true](http://blog.naver.com/PostView.nhn?blogId=takku04&logNo=150017480080&redirect=Dlog&widgetTypeCall=true)

