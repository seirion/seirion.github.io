---
layout: post
title:  "Elliptic-curve cryptography"
comments: true
date:   2019-11-08 22:01:00
---

타원곡선 암호

타원곡선 이론에 기반한 공개 키 암호 방식이다. 줄여서 ECC라고 쓰기도 한다. (한국어 wiki 에서 발췌)

[wiki](https://en.wikipedia.org/wiki/Elliptic-curve_cryptography)


## 필요한 산수

[Finite field(유한체)](https://en.wikipedia.org/wiki/Finite_field)

유한한 필드와 그 사이의 사칙연산에 대하여 닫혀있는 군

그 외 몇 가지 특징들이 있는데 여기서 중요한 점은 더하기와 곱셈의 정의이다.

[Fermat's little theorem](https://en.wikipedia.org/wiki/Fermat%27s_little_theorem)

유한체 내에서 곱셈의 역원을 구하기 위한 정리

[Secp256k1](https://en.bitcoin.it/wiki/Secp256k1)

비트코인에서 사용하는 곡선


## 타원 곡선 상의 덧셈 연산 정의

(* 이곳의 모든 이미지 출처는 다음 wiki 임)

https://en.wikipedia.org/wiki/Elliptic_curve

타원 곡선의 일반식은 다음과 같다.

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/3dbe6cab1bc2c7f1c99757dc6e5d7a517cf9b4f8)

a, b 값에 의해 곡선 모양이 결정된다.

![](https://upload.wikimedia.org/wikipedia/commons/thumb/d/d0/ECClines-3.svg/335px-ECClines-3.svg.png)

실수체에서는 곡선 상의 두 점 간의 덧셈을 그림과 같이, 두 점을 이은 직선이 만나는 세 번째 점과의 관계로 정의한다.

![](https://upload.wikimedia.org/wikipedia/commons/thumb/c/c1/ECClines.svg/680px-ECClines.svg.png)

타원 공식에 의해 보통 직선은 곡선과 세 점에서 만난다.

직선과 타원이 중근을 갖는 경우 두 점에서 만난다.

3 또는 4번의 경우 두 점의 합의 결과는 무한원점([point at infinity](https://en.wikipedia.org/wiki/Point_at_infinity))으로 정의한다.

이 무한 원점은 실수체에서 덧셈의 항등원이 된다.

그리고, 그림 3에서 보는 바와 같이, X 축에 대칭인 곡선 상의 점 두 개는 서로 덧셈의 역원이다.


### 서로 다른 두 점의 덧셈

서로 다른 두 점을 지나는 직선은 다른 한 점에서 곡선과 만난다.

예외는 위 그림과 같이 무한 원점과의 덧셈이다 - `P + 0 = P`

P=(x1,y2) and Q=(x2,y3) 라고 하고 나머지 한 점을 R(x3, y3) 이라고 하면,

이 직선의 기울기 `s = (y2 - y1) / (x2 - x1)` - (1)

따라서 직선의 방정식은 `y = s(x - x3) + y3` 이 된다. - (2)

(2) 식을 곡선 공식에 대입하면,

[참고](https://math.stackexchange.com/questions/2198139/elliptic-curve-formulas-for-point-addition)

(x - x1)(x - x2)(x - x3) = y - (4)

x3 = s^2 - x1 - x2

y3 = -s(x1 - x3) + y3


```kotlin
// y3 는 실제로는 -y3 를 바로 계산한 것이어서 부호가 반대가 됨
private fun addDifferentPoints(other: Point): Point {
    val x1 = x
    val y1 = y
    val x2 = other.x
    val y2 = other.y

    val s = (y2 - y1) / (x2 - x1) // slop
    val x3 = s * s - x1 - x2
    val y3 = s * (x1 - x3) - y1
    return Point(x3, y3, a, b)
}
```

### point doubling (동일한 두 점의 덧셈)

같은 점의 덧셈에서 직선의 기울기 s 는 미분 공식을 이용하여 간단히 산수한다.

2y dy = 3x^2 dx + a dx

s = dy/dx = (3x^2 + a) / 2y

나머지는 앞서 산수한 방식과 동일하다.

```kotlin
// y3 는 실제로는 -y3 를 바로 계산한 것이어서 부호가 반대가 됨
private fun addDifferentPoints(other: Point): Point {
    val three = BigInteger.valueOf(3)
    val two = BigInteger.valueOf(2)
    val s = (x * x * three) / (y * two) // s = (3x^2) / (2y)
    val x3 = s * s - (x * two)
    val y3 = s * (x - x3) - y
    return Point(x3, y3, a, b)
}
```


## 타원 곡선 상의 곱셈 연산 정의

[Elliptic curve point multiplication](https://en.wikipedia.org/wiki/Elliptic_curve_point_multiplication)

덧셈과 항등원 역원 등이 모두 정의되었으므로, 이를 이용하여 곱셈을 정의 할 수 있다.


## ECDSA

커브 연산을 위해 공개하는 기본 값들은 다음과 같다

* curve : a = 0, b = 7
* base point : G
    ```
    Gx = 0x79BE667EF9DCBBAC55A06295CE870B07029BFCDB2DCE28D959F2815B16F81798
    Gy = 0x483ADA7726A3C4655DA4FBFC0E1108A8FD17B448A68554199C47D08FFB10D4B8
    ```    
* integer order of G : n
    ```
    n = 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEBAAEDCE6AF48A03BBFD25E8CD0364141
    ```

### 키 생성

1. secure random 256 비트 값 생성 : ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/d76bb0d6e0b2b8b75f86e29901a2171d31250a39) 이것이 private key

2. ![](https://wikimedia.org/api/rest_v1/media/math/render/svg/6c81e45f784fc836e513c3929331a7c762fa4c87) 이것이 public key


### sign algorithm

```
uG + vP = kG       // P 는 public key, 즉, eG = P
vP = (k - u)G
P = ((k-u)/v)G
```
private key `e`를 알고 있으므로, P 를 eG 로 바꾸면
```
e = (k - u) / v
```
u 와 v 를 알고 있을 때, private key `e`를 찾아내는 문제는 [이산 로그(discrete logarithm)](https://en.wikipedia.org/wiki/Discrete_logarithm)문제이다.


해시 된 메세지 z 을 우리가 싸인 하려 할때,

다음과 같이 z, r 을 u, v 에 포함시킨다.
```
u = z/s, v = r/s
```
(r 은 랜덤 생성한 k로 kG 를 계산한 결과의 x 좌표 값, 즉 `(r, y) = kG`)


#### s 구하기
```
uG + vP = kG
uG + veG = kG   // P 를 eG 로 치환
u + ve = k      // G 제거
z/s + re/s = k  // u, v 를 z, s, r 에 대한 식으로 변경
(z + re)/s = k
s = (z + re)/k
```

이제 `z`(메세지 해시), `r`(kG의 x값), `e`(private key), `k`(랜덤값) 을 모두 알게 되었고,

과정이 바로 싸인 과정입니다. 싸인의 결과는 `(r, s)` 이다.


```kotlin
data class Signature(val r: BigInteger, val s: BigInteger)

fun sign(msg: ByteArray): Signature {
    val z = BigInteger(msg)
    val k = rng()
    val r = (k * G).x!!.num
    val kInv = FieldElement(k, n).multiplicativeInverse()
    var s = (z + r * priKey) * kInv % n
    if (s > n / BigInteger.valueOf(2)) s = n - s
    return Signature(r, s)
}
```

### verify algorithm

검증 과정은 public key 를 이용하여 서명으로부터 원래 메세지인 z 를 복원하는 과정이다.

서명 값은 `(r, s)` 이므로

```
uG + vP =           // 이 식에서 시작
(z/s)G + (r/s)P =   // P 를 eG 로 치환
(z/s)G + (re/s)G =
(z/s)G + (re/s)G =
(z+re)/sG
```
여기서 `s = (z + re)/k` 를 대입하면, 
```
((z+re)/s)G = kG
(z+re)/((z+re)/k)G = kG // 여기까지 좌변으로부터 kG 를 구할 수 있다 !
```
이렇게 해서, kG 포인트의 x 값이 s 와 일치함을 확인할 수 있다.

```kotlin
fun verify(msg: ByteArray, signature: Signature): Boolean {
    val z = BigInteger(msg)
    val sInv = FieldElement(signature.s, Secp256k1.n).multiplicativeInverse() // multiplicative inverse of s (s^-1)
    val u = z * sInv % Secp256k1.n
    val v = signature.r * sInv % Secp256k1.n
    val r = (u * Secp256k1.G) + (v * this)
    return r.x!!.num == signature.r
}
```

### Elliptic Curve Integrated Encryption Scheme (ECIES)

https://asecuritysite.com/encryption/ecc3

https://www.researchgate.net/publication/255970113_A_Survey_of_the_Elliptic_Curve_Integrated_Encryption_Scheme

https://www.shoup.net/papers/iso-2_1.pdf
