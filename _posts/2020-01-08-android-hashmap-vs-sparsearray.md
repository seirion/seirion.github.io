---
layout: post
title:  "Android HashMap vs SparseArray"
comments: true
date:   2020-01-08 07:13:00
---


안드로이드에서 `Integer` 타입을 키로하는 `HashMap`을 사용하면, 대신 [`SparseArray`](https://developer.android.com/reference/android/util/SparseArray)를 사용하라는 경고를 준다.

![](/images/2020-01-08/01-warnings.png)

성능이 더 좋다고 하는데 과연 어떻게 구현되었길래 그런 지 궁금해서 찾아 봤다.


## SparseArray

[안드로이드 문서](https://developer.android.com/reference/android/util/SparseArray)에는 `SparseArray` 가 메모리를 효율적으로 사용하고
[auto-boxing](https://docs.oracle.com/javase/tutorial/java/data/autoboxing.html)을 피할 수 있으므로 성능이 좋아진다고 말한다.

대신 구현의 특성 상 많은 수의 데이터를 저장하는 경우에는 적합하지 않으며, 수백개 정도의 item 을 저장하는 경우 성능 차이가 50% 미만이라고 쓰여져 있다.

## SparseArray 구현

`SparseArray`의 주요 멤버는 다음과 같다.
```java
private int[] mKeys;
private Object[] mValues;
private int mSize;
```
클래스 이름과 같이 기본적으로 데이터는 `Array`에 저장한다.

[`put`](https://developer.android.com/reference/android/util/SparseArray#put(int,%20E)) 메소드는 다음과 같다. 
```java
public void put(int key, E value) {
    int i = ContainerHelpers.binarySearch(mKeys, mSize, key);

    if (i >= 0) {
        mValues[i] = value;
    } else {
        i = ~i;

        if (i < mSize && mValues[i] == DELETED) {
            mKeys[i] = key;
            mValues[i] = value;
            return;
        }

        if (mGarbage && mSize >= mKeys.length) {
            gc();

            // Search again because indices may have changed.
            i = ~ContainerHelpers.binarySearch(mKeys, mSize, key);
        }

        mKeys = GrowingArrayUtils.insert(mKeys, mSize, i, key);
        mValues = GrowingArrayUtils.insert(mValues, mSize, i, value);
        mSize++;
    }
}
```

바이너리 서치를 통해 기존 값이 이미 있는 경우 key 의 index 를 얻어온다.
 
이 경우는 그냥 `mValue[i]`에 `value`값을 덮어쓰면 된다.


기존 key 값이 존재하지 않는 경우는 [upper bound](https://en.wikipedia.org/wiki/Upper_and_lower_bounds) 의 index 를 얻게 되는데,

이 index 는 바로 value 가 insert 될 지점이 된다.

따라서, 이를 통해 유추할 수 있는 것은 key 데이터가 Array(`mKeys[]`) 에 정열되어 저장되고,

새로운 key-value 값을 추가하기 위해서는 array copy 를 수행해야 한다는 점이다.

이 연산은 O(n) 의 [시간 복잡도](https://en.wikipedia.org/wiki/Time_complexity)를 가진다.

따라서 `HashMap.put` 의 시간 복잡도가 보통 O(1)인 것과 비교하여 성능이 더 나쁘다.



[`get`](https://developer.android.com/reference/android/util/SparseArray.html#get(int,%20E))메소드는 다음과 같다.

```java
private static final Object DELETED = new Object(); // 성능향상을 위한 삭제 표시 용 객체

public E get(int key, E valueIfKeyNotFound) {
    int i = ContainerHelpers.binarySearch(mKeys, mSize, key);

    if (i < 0 || mValues[i] == DELETED) {
        return valueIfKeyNotFound;
    } else {
        return (E) mValues[i];
    }
}
```

간단히 바이너리 서치를 통해서 index 를 구하고, 값을 찾아서 반환한다.

여기서 `DELETED`라는 객체가 등장하는데, 이는 약간의 성능 향상을 위해 사용되는 일종의 테크닉이다.

[`delete`](https://developer.android.com/reference/android/util/SparseArray.html#delete(int))메소드를 보면, 어떤 방식으로 사용되는 지 알 수 있다.

```java
public void remove(int key) {
    delete(key);
}

public void delete(int key) {
    int i = ContainerHelpers.binarySearch(mKeys, mSize, key);

    if (i >= 0) {
        if (mValues[i] != DELETED) {
            mValues[i] = DELETED;
            mGarbage = true;
        }
    }
}
```

실제로 지우는 것은 아니고, 그렇게 표시만 해 두는 것이다.

나중에 이렇게 표시한 곳에 새 데이터를 추가할 일이 있으면 Array copy 없이 바로 값을 할당할 수 있으므로,

이 경우에는 시간 복잡도가 O(log n)이 된다.

`DELETED`를 이용한 성능 계선에 대해 더 구체적으로 알고 싶다면 소스에서 `gc()`(private 메소드임) 등의 메소드를 더 찾아보면 쉽게 이해할 수 있다.

## 메모리 사용 비교

참고: [Exploring Java's Hidden Costs](https://academy.realm.io/posts/360andev-jake-wharton-java-hidden-costs-android/)

```
HashMap<K, V>                ArrayMap<K, V>
HashSet<K, V>                ArraySet<V>
HashMap<Integer, V>          SparseArray<V>
HashMap<Integer, Boolean>    SparseBooleanArray
HashMap<Integer, Integer>    SparseIntArray
HashMap<Integer, Long>       SparseLongArray
HashMap<Long, V>             LongSparseArray<V>
```


요약 하면, 데이터과 무관하게 `HashMap` 자체가 48바이트를 사용하고,

`HashMap`은 내부적으로 [load factor](https://en.wikipedia.org/wiki/Hash_table#Key_statistics) 를 가지는데

내부적으로 관리하는 bucket 크기에서 실제로 값이 차지하는 비율을 말한다.

이 비율이 높아질수록 [해시 충돌](https://en.wikipedia.org/wiki/Collision_(computer_science))이 발생할 가능성이 높으므로, 이 비율을 적정한 수준 아래로 유지하는 것이 중요한데 그 기준이 되는 값이다.

따라서, 이 여유분에 해당하는 공간은 낭비가 되는 셈이다.

기본값은 75% 로 되어 있다. 그러므로 많아야 전체 할당된 공간 중 3/4 만 실제 데이터로 채워졌다는 의미이다.

```java
static final float DEFAULT_LOAD_FACTOR = 0.75f;
```

`HashMap`에서 아이템을 다루기 위해 `Node`라는 객체 단위로 다루게 되는데 이것은 아이템 하나 당 32바이트를 차지한다.

그리고 value 를 저장하기 위한 공간으로 아이템 하나 당 4바이트가 필요하고, 위에 이야기했던 load factor 로 인해

약 1/4 만큼의 여유 공간을 추가로 요구한다.


한편, `SparseArray`는 아이템을 위한 공간을 제외한 객체의 크기는 32바이트를 차지하고,

아이템을 저장하기 위해 두 개의 배열을 가지는데, 하나는 key 를 위해서 나머지는 value 를 저장하기 위한 공간이다.

그러므로 각각 아이템 하나 당 4 + 4 바이트의 공간을 차지한다.

`SparseArray`에는 load factor 가 존재하지는 않지만, 구현 상의 이유로 항상 배열이 가득찬 상태가 아닐 수 있다.

여기서는 이런 비효율을 보수적으로 계산해서 약 75% 만큼만 값이 채워져 있다고 가장한다.


대략의 메모리 사용 비교는 다음과 같다.
```
SparseArray<V>
32 + (4 * entries + 4 * entries) / 0.75

HashMap<Integer, V>
48 + 32 * entries + 4 * (entries / loadFactor) + 8
```


아이템 50개로 가정하면 `HashMap`이 약 3배의 공간을 필요로한다. (656 vs 1922)


## 결론

`HashMap`의 `put`, `remove`, `get` 메소드의 시간 복잡도가 모두 O(1) 인데 비해

`SparseArray`는 각각 O(n), O(log n), O(log n) 으로 시간 복잡도 측면에서 성능은 다소 더 떨어지는 것으로 보인다.

하지만 수백 개 미만의 적은 item 을 사용하는 경우 n 이 작으므로 auto-boxing 과 hash 함수를 수행하는 등의 연산과 비교하면

그렇게 많은 성능 차이가 나지 않을 수 있다.


물론, `SparseArray`가 아낄 수 있는 메모리의 양도 어차피 item 의 개수가 적은 경우 그 영향이 작아질 수밖에 없는데

그런 면에서 굳이 이정도까지 메모리 최적화를 해야하는가라는 의문이 들기도 한다.

결국 사용자의 판단과 경험에따라 어떤 자료구조를 사용할 지 결정될 것이다.

