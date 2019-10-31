---
layout: post
title:  "RSA를 이해해 보자"
comments: true
date:   2019-10-31 02:01:00
---


## RSA

비대칭 키 알고리즘이며 이 이름은 알고리즘을 개발한 세 명의 이름을 딴 것. (Rivest–Shamir–Adleman) 왜 안 사전순 ?

https 나 공인인증서 등에 사용된다.


## 필요한 산수

[1. prime number](https://en.wikipedia.org/wiki/Prime_number)

[2. Modular arithmetic](https://en.wikipedia.org/wiki/Modular_arithmetic)

[3. Fermat's little theorem](https://en.wikipedia.org/wiki/Fermat%27s_little_theorem)

[4. Euler's totient function](https://en.wikipedia.org/wiki/Euler%27s_totient_function)

phi function 일반식 :

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/bb6b6388ded7d1e160a3bd82b60c5b593947088a)

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/db9ca515a95b78400d283a268dc9cd2db92be3a6)
means that the product is over all prime divisors of n.

대략의 해석 : n 의 약수인 소수 p 에 대한 모든 f(p) 곱

[5. Arithmetic function](https://en.wikipedia.org/wiki/Arithmetic_function#Notation)


## 키 생성

1. 서로 다른 소수 p, q 생성
2. n = pq 계산
3. φ(n) 계산 : [[4]](https://en.wikipedia.org/wiki/Euler%27s_totient_function)
4. e 찾기, 1 < e < φ(n) and gcd(e, φ(n)) = 1
5. d 계산, ed ≡ 1 (mod φ(n))
   d 는 모듈러 φ(n) 의 [modular multiplicative inverse](https://en.wikipedia.org/wiki/Modular_multiplicative_inverse)
   이 값은 [Extended Euclidean algorithm](https://en.wikipedia.org/wiki/Extended_Euclidean_algorithm) 으로 계산 가능,
   [[3]](https://en.wikipedia.org/wiki/Fermat%27s_little_theorem)을 이용해도 간단히 구해짐
   

(n, d) 는 private key, (n, e) 는 public key 가 된다.

이것 외 나머지 모든 값들은 버린다.


## 암호화 복호화 원리

메세지 m 을 e 로 암호화하여 c 를 얻어냄

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/fbfc70524a1ad983e6f3aac51226b9ca92fefb10)


c 에서 d 를 사용하여 원문을 계산함

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/10227461ee5f4784484f082d744ba5b8c468668c)


식의 모양을 보면 알 수 있듯, 지수 연산의 교환법칙이 성립하므로, d로 암호화한 것을 e로 풀 수 있다.

그래서 비대칭 암호화 !


## 증명

다음을 증명하고자 함

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/f72f4e12cb96fa6c740de9940360de534506b35e)

ed ≡ 1 (mod φ(n)) 이므로

ed ≡ 1 + h φ(n), h 는 음이 아닌 정수

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/577156a9d4b06941c54f394e1577b3356058916b)

[오일러의 정리](https://en.wikipedia.org/wiki/Euler%27s_theorem)에 의해

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/7d7e379ba0635438a23bf9cc46cbe07080b94113)


---

### 참고 : 오일러의 정리 요약

양의 정수 𝑛과 정수 𝑎에 관해, gcd(𝑎,𝑛)=1이면 다음이 성립한다.

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/2e818f3f88d3e71e569f171dd86f31e1903fdc55)
