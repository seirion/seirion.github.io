---
layout: post
title:  "bit operations & twiddling hacks"
comments: true
date:   2014-06-16 14:15:00
---

이 페이지는 bit twidding hacks 에 관한 내용으로, 다음 페이지의 내용 중 일부를 해석, 분석, 시험한 내용입니다.
[http://graphics.stanford.edu/~seander/bithacks.html](http://graphics.stanford.edu/~seander/bithacks.html)
 
 
참고 : 
two's complement
[http://en.wikipedia.org/wiki/Two's_complement](http://en.wikipedia.org/wiki/Two's_complement)



### Compute the integer absolute value (abs) without branching

```cpp
int v;           // we want to find the absolute value of v
unsigned int r;  // the result goes here 
int const mask = v >> sizeof(int) * CHAR_BIT - 1;  // CHAR_BIT == 8
 
r = (v + mask) ^ mask;
// or, r = (v ^ mask) - mask;
```

v 가 양수인 경우 (v = 10)

```cpp
v : 0x0000000A
mask : 0x0
v + mask : 0x0000000A
(v + mask) ^ mask : 0x0000000A
```

v 가 음수인 경우 (v = -10)

```cpp
v : 0xFFFFFFF6
mask : 0xFFFFFFFF
v + mask : 0xFFFFFFF5
(v + mask) ^ mask : 0x0000000A
```

2's complement 를 구하는 방법과 동일하다.

<br /><br />

### Determining if an integer is a power of 2

```cpp
unsigned int v; // we want to see if v is a power of 2
bool f;         // the result goes here 
 
f = (v & (v - 1)) == 0;
```

만약 v 가 0010 (2) 이라면,

```cpp
v : 0010 (2)
v - 1 : 0001 (2)
v & (v - 1) : 0
```

만약 v 가 0110 (2) 이라면,

```cpp
v : 0110 (2)
v - 1 : 0101 (2)
v & (v - 1) : 0100
```

v 가 0 이면 true 가 되는 문제가 있다. (별도 handling 필요)

```cpp
f = v && !(v & (v - 1));
```

이 연산은 정수의 가장 오른쪽 1 을 하나 지우는 것과 같다.
그러므로, 비트 가운데 1 이 하나만 있는 경우 (power of 2) 가장 오른쪽 비트를 지우면 0 이 된다.
따라서, 음수에는 안 된다. (예문은 검사할 변수가 unsigned 로 되어 있음)

<br /><br />

### Conditionally set or clear bits without branching

```cpp
bool f;         // conditional flag
unsigned int m; // the bit mask
unsigned int w; // the word to modify:  if (f) w |= m; else w &= ~m; 
 
w ^= (-f ^ w) & m;
```

조건 f 에 따라서 w 의 특정 비트 (m) 를 set 하거나 clear 하기 
여기서는 f 값이 true 인 경우 set, false 일 때 clear 한다.
 
```cpp
// f : 1 (true)
// m : 0011 (2)
// w : 0000 (2)
// ------------
// -f : 1111
// -f ^ w : 1111
// (-f ^ w) & m : 0011
 
// w ^ (-f ^ w) & m : 0011
// f : 1 (true)
// m : 0111 (2)
// w : 0010 (2)
// ------------
// -f : 1111
// -f ^ w : 1101
// (-f ^ w) & m : 0101
 
// w ^ (-f ^ w) & m : 0111
```

f 는 1 이고 -f 는 1111 이 된다.
여기에 (-f ^ w) 를 계산하면 w 의 모든 비트를 뒤집니다.
그 결과에 & m 을 계산하면, set 하려는 비트 중에 0인 비트에만 1 이 할당 된다.
따라서, 그 결과에 w 와 XOR 를 씌우면, 기존에 이미 set 된 비트는 그대로 1 로 남고, 0 이었던 비트도 1 로 set 된다.

<br /><br />

### Counting bits set (naive way)

```cpp
unsigned int v; // count the number of bits set in v
unsigned int c; // c accumulates the total bits set in v
 
for (c = 0; v; v >>= 1) {
  c += v & 1;
}
```

<br /><br />

### Counting bits set, Brian Kernighan's way

```cpp
unsigned int v; // count the number of bits set in v
unsigned int c; // c accumulates the total bits set in v
for (c = 0; v; c++) {
  v &= v - 1; // clear the least significant bit set
}
```

루프 한 번 마다 v & v - 1 을 하면 가장 오른쪽 비트(least significant bit)를 지운다.
gcc 에서는 내장 함수 __builtin_popcount() 를 사용하면 된다.


visual studio 2008 에서는 _mm_popcnt_u32(), _mm_popcnt_u64() 이런 게 있다. <nmmintrin.h> 필요.
참고 : [http://msdn.microsoft.com/en-us/library/bb514083(v=vs.90).aspx](http://msdn.microsoft.com/en-us/library/bb514083(v=vs.90).aspx)

<br /><br />

### Select the bit position (from the most-significant bit) with the given count (rank)

The following 64-bit code selects the position of the rth 1 bit when counting from the left. In other words if we start at the most significant bit and proceed to the right, counting the number of bits set to 1 until we reach the desired rank, r, then the position where we stop is returned. If the rank requested exceeds the count of bits set, then 64 is returned. The code may be modified for 32-bit or counting from the right.

```cpp
  uint64_t v;          // Input value to find position with rank r.
  unsigned int r;      // Input: bit's desired rank [1-64].
  unsigned int s;      // Output: Resulting position of bit with rank r [1-64]
  uint64_t a, b, c, d; // Intermediate temporaries for bit count.
  unsigned int t;      // Bit count temporary.
 
  // Do a normal parallel bit count for a 64-bit integer,                     
  // but store all intermediate steps.                                        
  // a = (v & 0x5555...) + ((v >> 1) & 0x5555...);
  a =  v - ((v >> 1) & ~0UL/3);
  // b = (a & 0x3333...) + ((a >> 2) & 0x3333...);
  b = (a & ~0UL/5) + ((a >> 2) & ~0UL/5);
  // c = (b & 0x0f0f...) + ((b >> 4) & 0x0f0f...);
  c = (b + (b >> 4)) & ~0UL/0x11;
  // d = (c & 0x00ff...) + ((c >> 8) & 0x00ff...);
  d = (c + (c >> 8)) & ~0UL/0x101;
  t = (d >> 32) + (d >> 48);
  // Now do branchless select!                                                
  s  = 64;
  // if (r > t) {s -= 32; r -= t;}
  s -= ((t - r) & 256) >> 3; r -= (t & ((t - r) >> 8));
  t  = (d >> (s - 16)) & 0xff;
  // if (r > t) {s -= 16; r -= t;}
  s -= ((t - r) & 256) >> 4; r -= (t & ((t - r) >> 8));
  t  = (c >> (s - 8)) & 0xf;
  // if (r > t) {s -= 8; r -= t;}
  s -= ((t - r) & 256) >> 5; r -= (t & ((t - r) >> 8));
  t  = (b >> (s - 4)) & 0x7;
  // if (r > t) {s -= 4; r -= t;}
  s -= ((t - r) & 256) >> 6; r -= (t & ((t - r) >> 8));
  t  = (a >> (s - 2)) & 0x3;
  // if (r > t) {s -= 2; r -= t;}
  s -= ((t - r) & 256) >> 7; r -= (t & ((t - r) >> 8));
  t  = (v >> (s - 1)) & 0x1;
  // if (r > t) s--;
  s -= ((t - r) & 256) >> 8;
  s = 65 - s;
```

<br /><br />

### Swapping values with XOR

```cpp
#define SWAP(a, b) (((a) ^= (b)), ((b) ^= (a)), ((a) ^= (b)))

// 즉, 이렇게 XOR 세 번 하면 바뀐다.
a ^= b;
b ^= a;
a ^= b;
``` 
 
<br /><br />

### Swapping individual bits with XOR


```cpp
unsigned int i, j; // positions of bit sequences to swap
unsigned int n;    // number of consecutive bits in each sequence
unsigned int b;    // bits to swap reside in b
unsigned int r;    // bit-swapped result goes here
 
unsigned int x = ((b >> i) ^ (b >> j)) & ((1U << n) - 1); // XOR temporary
r = b ^ ((x << i) | (x << j));
```

<br /><br />

### Compute modulus division by 1 << s without a division operator

```cpp
const unsigned int n;          // numerator
const unsigned int s;
const unsigned int d = 1U << s; // So d will be one of: 1, 2, 4, 8, 16, 32, ...
unsigned int m;                // m will be n % d
m = n & (d - 1);
```

d - 1 은 d 번째 부터 0111 이 된다.
즉 d 가 1000 이었다면 d - 1 은 0111 이다.
따라서 n 과 & 연산을 하면 하위 3 자리만 남는다.
 
<br /><br />

### Find the log base 2 of an integer with the MSB N set in O(N) operations (the obvious way)

```cpp 
unsigned int v; // 32-bit word to find the log base 2 of
unsigned int r = 0; // r will be lg(v)
 
while (v >>= 1) { // unroll for more speed...
  r++;
}
```

v 를 한 칸씩 shift 하면서 r 을 증가 시킴.
 
<br /><br />
 
### Find the integer log base 2 of an integer with an 64-bit IEEE float

```cpp
int v; // 32-bit integer to find the log base 2 of
int r; // result of log_2(v) goes here
union { unsigned int u[2]; double d; } t; // temp
 
t.u[__FLOAT_WORD_ORDER==LITTLE_ENDIAN] = 0x43300000;
t.u[__FLOAT_WORD_ORDER!=LITTLE_ENDIAN] = v;
t.d -= 4503599627370496.0; // 2 ^ 52
r = (t.u[__FLOAT_WORD_ORDER==LITTLE_ENDIAN] >> 20) - 0x3FF;
```

The code above loads a 64-bit (IEEE-754 floating-point) double with a 32-bit integer (with no paddding bits) by storing the integer in the mantissa while the exponent is set to 252. From this newly minted double, 252 (expressed as a double) is subtracted, which sets the resulting exponent to the log base 2 of the input value, v. All that is left is shifting the exponent bits into position (20 bits right) and subtracting the bias, 0x3FF (which is 1023 decimal). This technique only takes 5 operations, but many CPUs are slow at manipulating doubles, and the endianess of the architecture must be accommodated.
Eric Cole sent me this on January 15, 2006. Evan Felix pointed out a typo on April 4, 2006. Vincent Lefèvre told me on July 9, 2008 to change the endian check to use the float's endian, which could differ from the integer's endian.


이해 불가 ㅡㅡ;
 
<br /><br /> 
 
### Count the consecutive zero bits (trailing) on the right linearly

```cpp
unsigned int v;  // input to count trailing zero bits
int c;  // output: c will count v's trailing zero bits,
        // so if v is 1101000 (base 2), then c will be 3
if (v) {
  v = (v ^ (v - 1)) >> 1;  // Set v's trailing 0s to 1s and zero rest
  for (c = 0; v; c++)
  {
    v >>= 1;
  }
}
else { // 0 인 경우
  c = CHAR_BIT * sizeof(v);
}
```

v ^ v - 1 을 하면 가장 가장 오른쪽 비트 (lsb) 자리부터 1이 채워지고, 한 번 shift 하면 1 이 하나 지워진다.

```cpp
// v : 1100
// v - 1 : 1011
// v ^ v - 1 : 111
```

이 결과에서 shift 를 한 번 하면 11 만 남는다.
for 문은 1 의 개수를 센다.
gcc 라면 __builtin_ctz() 함수를 사용할 수 있다.
visual studio 에서는 _BitScanForward() 가 있다.

참고 : [http://msdn.microsoft.com/en-us/library/wfd9z0bb%28VS.80%29.aspx](http://msdn.microsoft.com/en-us/library/wfd9z0bb%28VS.80%29.aspx)
 
 
반대 방향으로 0 의 개수를 세는 함수는,
gcc 와 visual studio 에 대해서 각각 __builtin_clz(), _BitScanReverse() 가 있음.
 
<br /><br />

### Compute the lexicographically next bit permutation

Suppose we have a pattern of N bits set to 1 in an integer and we want the next permutation of N 1 bits in a lexicographical sense. For example, if N is 3 and the bit pattern is 00010011, the next patterns would be 00010101, 00010110, 00011001,00011010, 00011100, 00100011, and so forth. The following is a fast way to compute the next permutation.

```cpp
unsigned int v; // current permutation of bits 
unsigned int w; // next permutation of bits
 
unsigned int t = v | (v - 1); // t gets v's least significant 0 bits set to 1
// Next set to 1 the most significant bit to change, 
// set to 0 the least significant ones, and add the necessary 1 bits.
w = (t + 1) | (((~t & -~t) - 1) >> (__builtin_ctz(v) + 1));
```

 
또는,

```cpp
unsigned int t = (v | (v - 1)) + 1;  
w = t | ((((t & -t) / (v & -v)) >> 1) - 1);
``` 
 
복잡하군요 @.@
 
테스트 코드 : 

```cpp
#include <iostream>
 
using namespace std;
typedef unsigned int uint32;
 
void print(uint32 a) {
    for (int i = 31; i >= 0; i--) {
        int flag = a & (1 << i) ;
        if (flag) cout << 1;
        else cout << 0;
        if ((i) % 4 == 0) cout << " ";
    }
    cout << ": (" << a << ")" << endl;
}
uint32 next(uint32 v) {
    unsigned int w; // next permutation of bits
    unsigned int t = v | (v - 1); // t gets v's least significant 0 bits set to 1
    // Next set to 1 the most significant bit to change,
    // set to 0 the least significant ones, and add the necessary 1 bits.
    w = (t + 1) | (((~t & -~t) - 1) >> (__builtin_ctz(v) + 1));
    return w;
}
int main() {
    uint32 a = 3;
    print(a);
    for (int i = 0; i < 500; i++) {
        a = next(a);
        print(a);
    }
    return 0;
}
```

만약, 최고 값까지 다다른 다음에 한 번 더 호출하면 1 의 개수가 하나 줄어든 값이 튀어 나온다. (주의 !)
