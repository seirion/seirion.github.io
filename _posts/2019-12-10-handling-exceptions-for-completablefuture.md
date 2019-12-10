---
layout: post
title:  "Exception handling for CompletableFuture"
comments: true
date:   2019-12-10 22:01:00
---

`CompletableFuture` 에서 발생할 수 있는 예외를 처리하는 방법에 대해 이리저리 고민하다 글을 써 본다.


## 예외 처리

예외는 기본적으로 [`exceptionally()`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html#exceptionally-java.util.function.Function-) 에서 처리한다.

```kotlin
// kotlin
CompletableFuture
    .supplyAsync<Int> {
        throw NullPointerException("")
    }
    .exceptionally {
        return@exceptionally if (it.cuase is NullPointerException) 0 else -1
    }
```

`exceptionally()`는 [`Throwable`](https://docs.oracle.com/javase/8/docs/api/java/lang/Throwable.html) 타입을 받아서 정상 값을 반환하는 메소드를 파라미터로 받는다. 

보통 예외를 삼키는 방식으로 사용할 수 있다.


## 예외 통과 시키기

만약 특정 예외만 핸들링하고 나머지는 그대로 예외를 통과 시키고 싶은 경우에는 뭔가 귀찮은 작업을 해야 한다.

```kotlin
// kotlin
CompletableFuture
    .supplyAsync<Int> {
        throw NullPointerException("")
    }
    .exceptionally {
        return if (it.cuase is NullPointerException) {
            return@exceptionally 0
        } else {
            throw it.cause
        }
    }
```

간단하다. 하지만 이 간단한 것이 자바에서는 안 된다.

```java
// java
CompletableFuture.<Integer>supplyAsync(
    () -> {
        throw new NullPointerException("");
    })
    .exceptionally(t -> {
         if (t.getCause() instanceof NullPointerException) {
             return 0;
         } else {
             throw t.getCause(); // 컴파일 에러
         }
    });
```

자바에서 예외를 통과 시키려면 뭔가 복잡한 일을 해야 한다.

다음 예제와 같이 [`handle()`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html#handle-java.util.function.BiFunction-) 을 사용하여

값을 한 번 `CompletableFuture`로 감싼다. 이유는 정상 케이스와 예외 케이스를 모두 핸들링 하기 위해서다.

그 이후,`CompletableFuture` 를 unwrap 하여 원래 값으로 만든다.


```java
// java
CompletableFuture.<Integer>supplyAsync(
    () -> {
        throw new NullPointerException("");
    })
    .handle((v, t) -> {
        if (v != null) return CompletableFuture.completedFuture(v);
        if (t.getCause() instanceof NullPointerException) {
            return CompletableFuture.completedFuture(0);
        } else {
            return CompletableFuture.failedFuture(t.getCause()); // java9
        }
    })
    .thenCompose(a -> a); // CompletableFuture<CompletableFuture<Integer>> -> CompletableFuture<Integer>
``` 


결론 : 코틀린을 사용하자.
