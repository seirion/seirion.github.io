---
layout: post
title:  "JNA 사용 시 데이터 전달 방법"
comments: true
date:   2019-10-27 02:01:00
---

## 데이터 타입

[이곳](https://github.com/java-native-access/jna/blob/master/www/Mappings.md)에서 기본 타입에 대한 매핑을 볼 수 있다.

네이티브 api 를 호출할 때는 기본 타입을 이 매핑에 따라서 적절히 호출하며,

복잡한 객체는 json 등으로 serializing 하는 방법을 많이 사용하는 듯 하다.

String 타입은 쉽게 주고 받을 수 있으니 binary 데이터는 [base64](https://en.wikipedia.org/wiki/Base64) 등으로 인코딩하는 것도 한 가지 방법이다.


## 바이너리 데이터 받기


파라미터로 받은 `Pointer` 인스턴스를 이용하여 `getByteArray()` 메소드로 바로 받을 수 있다.

```java
    public void callback(Pointer value, int valueLen) {
        byte[] buffer = value.getByteArray(0, valueLen);
    }
```

## callback 호출에 의한 데이터 보내기

네이티브에서 callback 을 호출하여 Java 에서 pass-by-reference 에 의해 전달된 인자에 값을 전달해 주어야 하는 경우

JNA 에서는 [com.sun.jna.ptr package](https://github.com/java-native-access/jna/tree/master/src/com/sun/jna/ptr)의 타입을 사용한다.

`ByReference`타입을 상속하며, pointer 와 크기를 전달하는 방식으로 구현되어 있다.

정수를 전달하는 방법은 다음과 같다.

```java
    public void getSomeValue(String id, IntByReference value) {
        int v = findValueOf(id); // 값을 계산해 옴
        value.setValue(v);
    }
```

## 바이너리 데이터 전달하기

`Memory` 인스턴스를 데이터 크기 만큼 확보하고

`write()` 메소드를 이용하여 데이터를 복사한다.

```java
    public void getSomeBinary(String id,
                              PointerByReference recordValue,
                              IntByReference recordValueLen) {
        byte[] bytes = getByteDataOf(id); // 보낼 데이터 
        int byteLen = bytes.length;
        Pointer ptr = new Memory(byteLen);
        ptr.write(0, bytes, 0, bytes.length);
        recordValue.setValue(ptr);
    }
```



## 기타

데이터 전달을 위한 자세한 설명

http://java-native-access.github.io/jna/5.5.0/javadoc/overview-summary.html
