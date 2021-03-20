# 01. boolean functions and gate logic


## 1.1 boolean logic

불 대수: true/false, yes/no, on/off, 1/0 같은 2진수(bool) 값을 다루는 대수학.

bool function: 2진수를 입력받아 2진수를 출력하는 함수

컴퓨터는 2진수를 표현하고 처리하는 하드웨어이기 때문에 불 함수는 하드웨어 아키텍처의 중심.


### **Boolean Operation**
- `AND`, `OR`, `NOT`

### **Boolean expression**

```
NOT(0 OR (1 AND 1)) =
NOT(0 OR 1) =
NOT(1) =
0
```

general expression을 만들 수 있음

```
f(x, y, z) = (x AND y) OR (NOT(x) AND z)
```

이 함수가 어떤 함수인지 알기 위해서는
- 모든 x, y, z을 truth table로 만들어보면 된다.


## 1.2 Boolean Functions Synthesis

### **Boolean Identities**

특정 boolean expression이 같은지 확인

**commutative laws** (교환법칙)
- `(x AND y) = (y AND x)`
- `(x OR y) = (y OR x)`

**Associative laws** (결합법칙)
- `(x AND (y AND z)) = ((x AND y) AND z)`
- `(x OR (y OR z)) = ((x OR y) OR z)`

**Distributive laws** (분배법칙)
- `(x AND (y OR z)) = (x AND y) OR (x AND z)`
- `(x OR (y AND z)) = (x OR y) AND (x OR z)`

**De Morgan laws** (드모르간법칙)
- `NOT(x AND y) = NOT(x) OR NOT(y)`
- `NOT(x OR y) = NOT(x) AND NOT(y)`


이를 활용해서 boolean algebra를 풀 수 있음

```
NOT(NOT(x) AND NOT(x OR y)) =
# de morgan laws
NOT(NOT(x) AND (NOT(x) AND NOT(y))) = 
# associative laws
NOT((NOT(x) AND NOT(x)) AND NOT(y)) =
# idempotence laws (x AND x = x)
NOT(NOT(x) AND NOT(y)) =
# de morgan laws
NOT(NOT(x)) OR NOT(NOT(y)) =
x OR y
```

위 문제는 truth table을 만들어서도 풀 수 있다.

### Truth table to boolean function

우선 모든 1을 AND boolean expression으로 표현

해당 express들을 OR로 묶음

-> 모든 boolean function은 AND, OR, NOT operation만 사용한 expression으로 표현 가능

-> 정말 세가지가 다 필요할까? AND, NOT만으로도 가능하다.

```
(x OR y) = NOT(NOT(x) AND NOT(y)
```

-> `NAND` function

```
x NAND y = NOT(x AND y) -> NOT(x) OR NOT(y)
```

| x | y | NAND |
| 0 | 0 | 1    |
| 0 | 1 | 1    |
| 1 | 0 | 1    |
| 1 | 1 | 0    |

-> `NAND` operation만 사용해서 모든 boolean function을 표현할 수 있다.


#### 1. NOT(x) = (x NAND x)

```
NOT(x AND x) = NOT(x) OR NOT(x) = NOT(x)
```

#### 2. (x AND y) = NOT(x NAND y)

```
NOT(x NAND y) = NOT(NOT(x AND y)) = NOT(NOT(x) OR NOT(y)) = x AND y
```

```
(x NAND y) NAND (x NAND y) = (NOT(x) OR NOT(y)) NAND (NOT(x) OR NOT(y))
= NOT(NOT(x) OR NOT(y)) OR NOT(NOT(x) OR NOT(y))
= (x AND y) OR (x AND y)
= x AND y
```

### 3. (x OR y) = NOT(x) NAND NOT(y)

(x NAND x) NAND (y NAND y)

```
NOT(x) NAND NOT(y) = NOT(NOT(x) AND NOT(y)) = x OR y
```


## 1.3. Logic Gates

Gate Logic

- logic gates를 통해 boolean function을 구현하는 테크닉
- Logic gates란?
  - Nand, And, Or, Not같은 기본 게이트
  - Mux, Adder같은 composite 게이트

### elementary logic gates

#### Nand

- gate diagram: (사진)

- functional specification:

```
if (a==1 and b==1) then out=0 else out=1
```

- truth table

| x | y | NAND |
| 0 | 0 | 1    |
| 0 | 1 | 1    |
| 1 | 0 | 1    |
| 1 | 1 | 0    |


#### And, Or, Not

**And**

```
if (a==1 and b==1) then out=1 else out=0
```

**Or**

```
if (a==1 or b==1) then out=1 else 0
```

**Not**

```
if (in==0) then out=1 else 0
```

#### Composite gates

여러개의 elementary logic gates를 조합하여 만든 게이트.

아래 게이트는 3개의 input을 받아서 1개의 output을 반환. 두개의 and gate로 만들 수 있다.

```
if (a==1 and b==1 and c==1) then out=1 else 0
```


#### Gate Interface / Gate Implementation

Interface answer the question, what.

Implementation answer how the chip is doing.


Interface의 기능을 설명하는 방법은 딱 하나임.
Only one unique way to describe the function.

구현하는 방법은 여러가지.

`One obstruction, many different implementations.`

#### Circuit implementatinos

## 1.4. Hardware Description Language

### Design: from requirements to interface

Requirement: truth table & diagram

이를 바탕으로 HDL을 작성 가능

NAND gate로부터 시작할 수 있지만, And, Or, Not gate를 만들었다고 가정하고 시작.

#### Xor

| a | b | out |
| 0 | 0 |  0  |
| 0 | 1 |  1  |
| 1 | 0 |  1  |
| 1 | 1 |  0  |

**General idea를 생각해내기**

```
out=1 when
a AND NOT(b)
OR 
NOT(a) AND b
```

**design diagrams**

(Xor diagram 추가)

HDL은 단순히 diagram의 description

- 각 칩의 in, out을 명시적으로 적고
- 이를 적어줌

```
/** Xor gate: out = (a Aand Not(b)) Or (Not(a) And b)) */

CHIP Xor {
  IN a, b;
  OUT out;
  
  PARTS:
  NOT(in=a, out=nota)
  NOT(in=b, out=notb)
  AND(a=a, b=notb, out=aAndNotb)
  AND(a=b, b=nota, out=notaAndb)
  OR(a=aAndNotb, b=notaAndb, out=out)
}
```

- HDL: functional / declarative language
- The order of HDL statement is insignificant -> but typically left to right
- Interface
```
Not(in= , out=), And(a=, b=) ...
```

## 1.5 Hardware Simulation

It's hard to know HDL program is right.

Simulation program을 사용해서 검증 가능.

## 1.6. Multi-bit Buses
- sometimes we manipulate "together" an array of bits
- 이 그룹을 하나의 entity로 그룹지어 생각하는게 편하다:  "bus"라는 용어
- HDL은 이 buses를 다루는 notation들을 제공함

예시)

Addition of two 16-bit integer

```
CHIP Add16 {
    IN a[16], b[16];
    OUT out[16];
    
    PARTS:
}
```

세개의 16-bit value를 더하는 hdl.

위에서 만든 Add16을 사용하여 temp를 만들고 이를 out시킬 수 있음

```
CHIP Add3Way16 {
    IN first[16], second[16], [third[16];
    OUT out[16];
    
    PARTS:
        Add16(a=first, b=second, out=temp);
        Add16(a=temp, b=third, out=out);
}
```

4개의 16-bit를 더하는 hdl.

4개의 모든 bits를 input으로 함께 받을 수 있따.

이런 방식을 Multi-way chips라고 부름

```
CHIP AND4Way {
    IN a[4];
    OUT out;
    
    PARTS:
        AND(a=a[0], b=a[1], out=t01);
        AND(a=t01, b=a[2], out=t02);
        AND(a=t02, b=a[3], out=out);
}
```

다른 예시)

```
...
IN lsb[8], msb[8], ...
...
Add16(a[0..7]=asb, a[8..15]=msb ...);
```

요부분은 문서 한번 읽어봐야될듯..


## 1.7 Project 1 overview

신이 내리신(?) `Nand` 게이트만을 이용해서 15개의 게이트 만들 예정

### Elementary logic gates
- Not
- And
- Or
- Xor
- Mux
- DMux

### 16-bit variants
- Not16
- And16
- Or16
- Mux16

### Multi-way variants
- Or8Way
- Mux4Way16
- Mux8Way16
- DMux4Way
- DMux8Way


위에서 익숙하지 않은 것들 정리

### Multiplexor

3개의 input이 옴. a, b, sel

```
if(sel==0)
  out=a
else
  out=b
```
- truth table은 a,b,sel 8가지 / sel 자체의 truth table
- selecting, outputting 두 가지를 가능하게 함

예시) using mux logic to build a programmable gate

AndMuxOr

```
if (sel==0)
  out = (a And b)
else
  out = (a Or b)
```

```
CHIP AndMuxOr {
    IN a, b, sel;
    OUT out;
    
    PARTS:
    And (a=a, b=b, out=aAndb);
    Or (a=a, b=b, out=aOrb);
    Mux (a=aAndb, b=aOrb, sel=sel, out=out);
}
```

**Multiplexer implementation**

tip: And, Or, Not만 가지고 만들 수 있음


### Demultiplexor

Multiplexor의 반대. distributor라고 생각하면 됨.

```
if (sel==0)
  {a,b}={in,0}
else
  {a,b}={0,in}
```

예시) Multiplexing / demultiplexing in communication networks


### And16

16bit variants 중 하나

16bit의 intput을 받음

ex)

```
  a = 1 0 1 0 1 0 1 1 0 1 0 1 1 1 0 0
  b = 0 0 1 0 1 1 0 1 0 0 1 0 1 0 1 0
out = 0 0 1 0 1 0 0 1 0 0 0 0 1 0 0 0
```

```
CHIP And16 {
  IN a[16], b[16];
  OUT out[16];
  ...
}
```


### Mux4Way16

4 16bit intput, 2 bit sel


### Project 1

Given: Nand

Goal: Build 15 gates


---

## project 1

### Mux




(a And Not(sel))
Nand
(b And sel)

Not((a And Not(sel)) and (b And sel))
