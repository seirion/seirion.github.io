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




### Elliptic Curve Integrated Encryption Scheme (ECIES)

https://www.researchgate.net/publication/255970113_A_Survey_of_the_Elliptic_Curve_Integrated_Encryption_Scheme

https://www.shoup.net/papers/iso-2_1.pdf
