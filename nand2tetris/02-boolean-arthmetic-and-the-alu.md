# 02 boolean arthmetic and the alu

## 2.1. Binary numbers

2진법에 대한 간단한 설명. 자세한 내용은 생략


## 2.2. Binary addition

이진법을 더하는 법. 10진수처럼 더하면 된다.

다만 할당된 비트 (ex. 8bit + 8bit)를 넘어서는 carry는 overflow로 처리된다.

3가지 형태의 adder를 만들어볼 예정

### Half adder

두 bit를 더함. 

a, b를 더해서 sum(값)과 carry가 나옴

```
| a | b | sum | carry |
| 0 | 0 | 0   | 0     |
| 0 | 1 | 1   | 0     |
| 1 | 0 | 1   | 0     |
| 1 | 1 | 0   | 0     |
```

### Full adder

위의 half adder에서 이전 계산에서 나온 carry도 받는 adder

a, b, c(이전 carry)를 받아서 sum, carry를 리턴한다

총 진리표를 그려보면 8가지가 나옴

```
| a | b | c | sum | carry |
| 0 | 0 | 0 | 0   | 0     |
...
| 1 | 1 | 1 | 1   | 1     |
```


### Multi-bit Adder

두 full number를 더해서 output을 내보내는 adder.

16-bit adder는 16bit a, b를 받아 16bit output을 반환한다.


## 2.3. Negative Numbers

4 bits로 나타낼 수 있는 양수는 0~15 이다. (0000, 0001, ... 1111)

4bit로 음수를 나타는 데에는 여러가지 방법이 있다.

가장 간단한 방법은 음수일 경우 가장 첫번째 bit를 1로 바꾸는 방법

ex.)
```
0001 -> 1
1111 -> -1
0010 -> 2
1010 -> -2
...
```

하지만 위 방법은 여러가지 이유로 쓰이지 않음. 위 경우라면 `1000`이 `-0`이 되어 구현시에 매번 이를 처리해주어야됨.

그래서 쓰이는 법은 `2의 보수법`

> 음수 -x를 "2의n승 - x" (`2**n - x`)로 표현하는 방법

```
0000 0
0001 1
0010 2
0011 3
0100 4
0101 5
0110 6
0111 7
1000 -8 (8)
1001 -7 (9)
1010 -6 (10)
1011 -5 (11)
1100 -4 (12)
1101 -3 (13)
1110 -2 (14)
1111 -1 (15)
```

위에서 음수 3을 구하려면 2의 4승에서 3을 뺀 13이 음수3이 된다. (`2**4 -3`) 


### 2의 보수의 장점

***더하기 가능***

`-2 + -3`

```
  -2 -> 14 -> 1110
+ -3 -> 13 -> 1101
```


`1110 + 1101 = 11011`이지만, 11011은 4bit를 넘으므로 overflow를 제외한 `1011`인 `11`을 값으로 쓴다.

`1011` -> `11` -> `-5` (-2 + -3)


### Computing -x

_ Input: x
_ Output: -x (in 2s complement)

```
2**n - x = 1 + (2**n -  1) - x
```

`2**n - 1`은 `111111..` 이므로 x를 빼고 마지막에 1만 더해주면 됨.

ex. 4의 음수인 -4를 구하려면?

4: `0100`

- 1. 1111에서 4(0100)를 빼줌: `1111 - 0100 = 1011`
- 2. 위해서 구한 값(`1011`)에서 1을 더해줌: `1011 + 1 = 1100` -> 우리가 구하려던 값 (12)이 나온걸 알 수 있음

## 2.4. Arithmetic Logic Unit (ALU)

폰 노이만 다이어그램을 보면 CPU의 핵심이 ALU인 것을 볼 수 있음.

> ALU는 두 input을 받아 output을 내보내 주는 function

- Arithmetic operations: interger addition, multiplication, division...
- logical operations: And, Or, Xor ...

### The Hack ALU

- Operators on 2 16-bit 2's complement values
- Outputs: 16-bit 2's complement values
- Which function to compute: six 1-bit bit
- Compute one out of a family of 18 functions
- Also output two 1-bit values (`zr`, `ng`) -> 이후에 설명할 에정

ALU가 함수를 실행시키게 하려면 control bit를 정해진 binary combination에 맞게 세팅하면 됨.

![image](https://user-images.githubusercontent.com/23415251/112174059-73472900-8c39-11eb-90ad-981dd665ff69.png)

> 완전한 진리표는 2의 6승인 64개이지만 위 truth table은 자주 쓰이는 18개만 표시

### The Hack ALU operation

pre-setting the x input
- zx: if zx then x = 0
- nx: if nx then x = !x

pre-setting the y input
- zy: if zy then y = 0
- ny: if ny then y = !y

selecting between computing + or &
- f: if f then out=x+y else out=x&y

post-setting the output
- no: if no then out=!out

위의 6가지 operation을 실행시키면 out이 나옴

ex 1) `!x`

```
zx = 0
nx = 0
zy = 1
ny = 1
f = 0
no = 1
```

위 operation은 `!x` out이 되는데, 이를 직접 해보면 다음과 같음

**Input**:

- x: 1 1 0 0
- y: 1 0 1 1

**Following pre-settings**:

- x: zx, nx가 모두 0이기 때문에 x는 그대로 `1 1 0 0`
- y: zy, ny가 모두 1이기 때문에 y는 모두 !0인 `1 1 1 1`

**Computing and post-setting**:
- f = 0 이기 때문에 `x&y` -> `1 1 0 0`
- no = 1 이기 떄문에 위에서 !(x&y)가 되어 -> `0 0 1 1`

결과적으로 `!x`와 같은 값이 나온다


ex2) `y-x`

```
zx = 0
nx = 0
zy = 0
ny = 1
f = 1
no = 1
```

**Input:**

- x: 0 0 1 0 (2)
- y: 0 1 1 1 (7)


**Following pre-settings**:

- x: zx, nx가 모두 0이기 때문에 x는 그대로 `0 0 1 0`
- y: zy=0, ny=1이기 때문에 y는 !y -> `1 0 0 0`

**Computing and post-setting**:

- f = 1이기 때문에 `x+y` -> `0 0 1 0 + 1 0 0 0 = 1 0 1 0`
- no = 1이기 때문에 !(x+y) -> `0 1 0 1`(5)
- 결론적으로 `y-x`(7-2)인 5(`0 1 0 1`)가 나온다


ex3) 위 18개 진리표에 나오지 않은 로직의 결과는?

```
zx = 1
nx = 0
zy = 1
ny = 1
f = 1
no = 1
```

- x: 0 0 1 1 (3)
- y: 0 1 1 0 (6)


- x: zx=1, nx=0 -> x = `0 0 0 0`
- y: zy=1, ny=1 -> y = !0 = `1 1 1 1`
- f=1 -> x+y -> `1 1 1 1`
- no=1 -> !(x+y) -> `0 0 0 0`

-> 위 로직 조합은 `0`의 결과가 나옴

### The Hack ALU output control bits

#### zr, ng

if out == 0 then zr = 1, else zr = 0
if out < 0 then ng = 1, else ng = 0

왜 위 두 비트가 필요한지?

### Perspective

The Hack ALU is
- simple
- elegant
- easy to implement
  - set 16-bit value to 0000000000000000
  - set 16-bit value to 1111111111111111
  - Negate 16-bit value (bit-wise)
  - Compute + or & on two 16-bit values
  - That's all!

## Project 2 overview

- Given: project 1에서 만든 칩들
- Goal: 다음 칩들을 제작
  - HalfAdder
  - FullAdder
  - Add16
  - Inc16
  - ALU


### HalfAdder
- input: a, b
- out: sum, carry

Tip: 두개의 기초적인 게이트로 구현 가능
- sum: xor
- carry: and

![image](https://user-images.githubusercontent.com/23415251/112183039-32531280-8c41-11eb-8b18-d26bd99b67f6.png)


### FullAdder
- input: a, b, c
- out: sum, carry

Tip: 두개의 half-adder로 구현 가능

![image](https://user-images.githubusercontent.com/23415251/112183115-4bf45a00-8c41-11eb-95bd-320d9f24e7e5.png)


### 16-bit adder

- input: a[16], b[16]
- out: out[16]

Tip
- 16 full adder의 sequence로 이뤄짐
- carry bit는 right to left로 "piped"됨
- overflow는 무시하면 됨


### 16-bit incrementor
1을 증가시켜주는 칩. overflow는 무시

- input: a[16]
- out: out[16]

Tip:
- single-bit 0, 1도 hdl로 구현 가능? (true, false)

### ALU

Tip:
- Building blocks: Add16과 project 1에서 만든 칩들
- 20줄 이하의 HDL 코드로 만들 수 있음..!
