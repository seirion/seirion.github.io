---
layout: post
title:  "data class 상속"
comments: true
date:   2019-11-02 00:01:00
---


## [data class](https://kotlinlang.org/docs/reference/data-classes.html)는 final

data class 는 기본으로 final 이므로 상속이 안 된다.

대신 Java 에서는 안 되지만 kotlin 에서는 interface 를 이용하여 대략 다음과 같이 필드를 상속하는 게 가능하다.

단, 상속받을 필드에 `override` 키워드가 필요하다.


```kotlin
interface Person {
    val name: String
    val age: Int
    val email: String
}

data class Adult(
    override val name: String,
    override val age: Int,
    override val email: String,
    val isMarried: Boolean = false,
    val hasKids: Boolean = false
) : Person {

data class Child(
    override val name: String,
    override val age: Int,
    override val email: String = ""
) : Person
```

references: [Kotlin data classes — enough boilerplate](https://proandroiddev.com/kotlin-data-classes-enough-boilerplate-c4647e475485)
