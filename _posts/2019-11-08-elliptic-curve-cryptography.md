---
layout: post
title:  "Elliptic-curve cryptography"
comments: true
date:   2019-11-08 22:01:00
---

## Elliptic-curve cryptography

[wiki](https://en.wikipedia.org/wiki/Elliptic-curve_cryptography)


## 필요한 산수

[Finite field(유한체)](https://en.wikipedia.org/wiki/Finite_field)

유한한 필드와 그 사이의 사칙연산에 대하여 닫혀있는 군

그 외 몇 가지 특징들이 있는데 여기서 중요한 점은 더하기와 곱셈의 정의이다.

[Fermat's little theorem](https://en.wikipedia.org/wiki/Fermat%27s_little_theorem)

유한체 내에서 곱셈의 역원을 구하기 위한 정리

[Secp256k1](https://en.bitcoin.it/wiki/Secp256k1)

비트코인에서 사용하는 곡선


## 타원 곡선 상의 점과 연산

(* 이곳의 모든 이미지 출처는 다음 wiki 임)
https://en.wikipedia.org/wiki/Elliptic_curve

타원 곡선의 일반식은 다음과 같다.

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/3dbe6cab1bc2c7f1c99757dc6e5d7a517cf9b4f8)

a, b 값에 의해 곡선 모양이 결정된다.

![](https://upload.wikimedia.org/wikipedia/commons/thumb/d/d0/ECClines-3.svg/335px-ECClines-3.svg.png)

실수체에서는 곡선 상의 두 점 간의 덧셈을 그림과 같이, 두 점을 이은 직선이 만나는 세 번째 점을과의 관계로 정의한다.

![](https://upload.wikimedia.org/wikipedia/commons/thumb/c/c1/ECClines.svg/680px-ECClines.svg.png)

타원 공식에 의해 보통 직선은 곡선과 세 점에서 만난다.

직선과 타원이 중근을 갖는 경우 두 점에서 만난다.

3 또느 4번의 경우 두 점의 합의 결과는 무한원점([point at infinity](https://en.wikipedia.org/wiki/Point_at_infinity))으로 정의한다.
