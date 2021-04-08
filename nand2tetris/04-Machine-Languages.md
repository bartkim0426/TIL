# 04. Machine Languages

## 4.1. Machine languages: Overview

컴퓨터가 다른 machine과 다른점: universial

튜링 머신 -> 폰 노이만이 구현

하드웨어는 고정되어있고, 소프트웨어(program)가 변경되면서 다양한 기능을 할 수 있게 만들어줌

실제 컴퓨터에서는 memory 안에 프로그램이 있어서 CPU가 어떤 일을 해야할 지 알려줌


### Machine Language

Program: sequence of instruction -> CPU에 전달

3가지 element로 구성됨

1. Operations: 어떻게 instruction을 specify 하는지?
2. how do we know which instruction to perform at any given stage and time?
3. Addressing: what to operate on

### Compilation

high-level language (python, java)를 machine language로 complie (translate)

- 실제로 프로그래머들이 사용하지는 않음
- computer 입장에서는 모든 프로그램을 machine language로 받아들임

### Mnemonics

크게 두가지로 구현

- Instruction
  - sequences of bits
  - readable 하게 쓰여짐

ex
- 010001000110010 -> `0100010` + `0011` + `0010`
- 0100010: ADD
- 0011: R3
- 0011: R2

interpretation: 2가지 방법
- 1. synthetic form: computer에 존재하는 실재 bit에 대해 지칭
- 2. assembly language -> translate to "bit" via "Assembler"

### Symbols

- Machine Language: `010001000110010`
- Assembly language: `ADD 1, Mem[129]`
  - 많은 assembly language는 메모리에 접근할 수 있어야됨
  - location 129 in memory holds the "index"
  - 이 경우, symbol을 사용하여 이를 나타낼 수 있다.
    - "Symbolic assembler"는 "index"를 `Mem[129]` location으로 translate 해준다고 하면
    - `ADD 1, index`로 사용할 수 있음


## 4.2. Machine Languages: Elements

### Machine Language
- Specification of the Hardward/Software interface
  - what are the supported operations?
  - what do they operate on?
  - How is the program controlled?
- Usually in close correspondence to actual Hardware architecture
- Cost-Performance tradeoff
  - silicon area
  - time to complete instruction

### 1. Machine Operations

- Usually correspond to what's implemented in hardware
  - Arthmetic operations: add, subtract, ...
  - Logic operations: and, or, ...
  - Flow control: where to jump: "goto instructionX"...

- Difference between machine languages
  - richness of the set of operations
  - data types (width of bits, floating point)

### 2. Addressing

### Memory hierachy
- Accessing memory location: expensive
  - need to supply long address
  - getting memory contents into the CPU take time
- Solution: Memory Hierarchy
  - 여러 memory 계층을 둠
    - 실제 memory
    - 더 빠르고 효율적인 메모리: cache memory
    - CPU 안에 memory: registers

![image](https://i.imgur.com/2SroR8B.png)

### Registers

- 대부분의 cpu는 few, easily accessed "register" 를 가지a
- 이 레지스터의 숫자와 기능이 machine language의 핵심 파트

두가지 종류가 있음

**Data register**

- 데이터를 register에 저장
  - data registers: Add R1, R2
    - R1: 10
    - R2: 35

**Address Register**
- 메인 메모리의 address 주소를 저장
  - address register: Store R1,@A
    - A: 137
    - R1: 77
    - 77이 137번째 메모리에 저장됨

### Addressing Modes

데이터의 address 선택하는 방법이 여러가지

- Register
  - Add R1, R2
- Direct
  - Add R1, M[200]
    - register 1, main memory 200th
- Indirect
  - Add R1,@A
    - 직접 사용하는 대신 register 안에 저장된 address 사용
- Immediate
  - Add 73, R1
    - machine language에 직접 명시 가능

### Input / Oupt

많은 input/output device가 있음: keyboard, mouse, camera ...

- 연결하는 방법: register를 device에 연결
  - ex. Memory[12345]가 마우스의 마지막 움직임을 가지고 있음
- CPU는 이들과 소통하는 protocol이 필요
  - software drivers가 이 프로토콜을 알고있음

### 3. Flow Control

- 보통 CPU는 instruction을 순서대로 실행함
- Unconditional jump
  - 다른 location으로 unconditionally "jump"를 하기도 함
    - loop할때 주로 그렇게 함

![image](https://i.imgur.com/skSfwnu.png)

이를 위해서는 해당 메모리 주소를 기억하고 있어야 됨
- special symbol을 사용함. ex. loop

![image](https://i.imgur.com/55mF3MB.png)

- Conditional Jump
  - 특정 조건에서만 "jump" 가 필요하기도 함

![image](https://i.imgur.com/lGBnXHz.png)


## 4.3. The HACK Computer and Machine Language

### Hack computer: hardware

3가지 main element를 가진 hack computer를 만들예정 (16-bit machine)
- Data memory (RAM)
  - sequence of 16-bit registers
  - RAM[0], RAM[1], ...
- Instruction memory (ROM)
  - sequence of 16-bit registers
  - ROM[0], ROM[1], ...
- CPU(Central Processing Unit)
  - perform 16-bit instruction
- Instruction bus / data bus / address buses
  - cpu, instruction memory, data memory를 연결

### Hack computer: software

Hack machine language:
- 16-bit A-instructions
- 16-bit C-instructions

Hack program
- sequence of instructions written in Hack machine language

### Hack computer: control

Control:
- ROM is loaded with HACK program
- if reset button is pushed
- program starts running

### Hack computer: registers

Hack machine language는 3 register를 인식
- D holds a 16-bit values
- A holds a 16-bit values
- M: selected memory register
  - 딱 한개만 선택됨 (convention으로 부름)
  - 16-bit RAM register addressed by A

![image](https://i.imgur.com/5jfMPI3.png)

### The A-instruction

**syntax**: `@value`
- value is either
  - non-negative decimal constance or
  - symbol referring

**semantics**
- set a A register to value
- side effect: RAM[A] become the selected RAM register (M)

**example**

```
@21
```
- set A register to 21
- RAM[21] -> become selected RAM register (M)

Usage example:

```
// Set RAM[100] to -1
@100   // A=100
M=-1   // RAM[100]=-1
```
- 항상 memory에 operate 하기 전에 해당 주소의 memery를 select 해야됨
- A = Addressing


### The C-instruction

```
// destination, computation, jump directive
dest = comp ; jump
```

> dest, jupm는 optional.

- 먼저 compute를 해서
- 이를 destination에 저장하거나,
- 이를 사용해서 다른 instruction으로 jump

**comp 목록**
- 0, 1, -1, !A, ...

**dest 목록**
- 8가지
- M은 RAM[A]를 지칭

**jump**
- 8가지 condition
- computation의 결과와 0를 비교함

**Semantics**
- comp의 결과를 계산
- 이를 dest에 저장
- (comp jump 0)가 true이면 ROM[A]에 저장된 instruction으로 jump

**example**

```
// Set the D register to -1: comp = -1, dset = D
D = -1

// Set RAM[300] to the value of D register minus 1
// comp: D-1, dest: RAM[300]
@300  // A=300 : must use a instruction to select memory
M=D-1 // M refrest RAM[A], RAM[300]=D-1
```

> 메모리에 접근하기 위에서는 A instruction으로 레지스터에 선택해야함

```
// If (D-1==0) jump to execute the instruction stored in ROM[56]
@56   // A=56
D-1;JEQ  // if (D-1 == 0) goto 56
// JEQ: Jump if Equal to 0
```

> unconditional jump는 convention으로 `0;JMP`를 사용함

연습문제

```
@1   // set A register value to 0
M=A-1;JEQ
// M = RAM[1] = A-1 = 0
// JEQ: Jump if result (M) is 0 -> to A (instruction 1)
```

## 4.4. Hack Language Specification

### The Hack macine language

두가지 방법으로 작성 가능
- binary code
- symbolic language

```
# symbolic
@17
D+1;JLE

# translate to binary
000000000010001
111001111000110

# and execute
```

### The A-instruction

#### Set the A registre to value

**Symbolic syntax**

```
@value

# 예시
@21
```
value는 non-negative decimal constant(`<= 32767(=2**15 - 1)`) or symbol reffering

**binary syntax**

```
0value

# 예시: 0 + 21(binary)
0000000000010101
```

### The C-instruction

**Symbolic syntax**

```
dest = comp ; jump
```

**Binary syntax**

```
1 1 1 a c1 c2 c3 c4 c5 c6 d1 d2 d3 j1 j2 j3
```
- 첫번째 bit ([0]): op code
  - A, C instruction인지 알려줌 (0: A, 1: C)
- 2, 3번쨰 bits ([1:2]): 사용되지 않음
- 그 이후 7 bits ([3:9]): comp bits
- 그 이후 3 bits ([10:12]): dest bits
- 그 이후 3 bits ([13:15]): jump bits

![image](https://i.imgur.com/iMjffxh.png)

**computation syntax**

```
# [3:9]
... a c1 c2 c3 c4 c5 c6 ...
```

a가 0, 1인 경우 각각 c1~c6까지의 값에 따라서 operation이 결정됨

![image](https://i.imgur.com/b6QiRLy.png)

ex. D+1

a = 0, 나머지는 `011111` -> `001111`

**Destination syntax**

```
# [10:12]
... d1 d2 d3 ...
```

8가지 destination이 있음.

![image](https://i.imgur.com/8SMW8ES.png)

**Jump syntax**

destination과 거의 동일. 8가지 jump가 있음

![image](https://i.imgur.com/dhk0b7W.png)


**dest, comp, jump 모두 정리**

![image](https://i.imgur.com/DQE7Ltf.png)


ex. 1111010101101100

```
// 1 11 1 010101  101 100

AM = D|M; JLT
```

### Hack program

이제 binary와 symbolic instruction을 알았으니 프로그램을 작성할 수 있다. 

Observations:
- Hack program은 Hack instructions들의 집합
- 가독성을 위해 공백이 허용됨
- 주석이 허용됨
- 더 좋은 방법은 다음 강의들에서 설명할 예정

작성된 코드는 binary code로 translate 되어 실행된다.


## 4.5. Input / Output

기존의 ROM, CPU, RAM으로 구성된 Hack computer platform에 추가적인 I/O: Peripheral I/O devices
- user에게 데이터를 받고, 보여주는데 사용됨
- keyboard: input
- screen: output

어떻게 이런 I/O에 접근할 수 있을까?

High-level 접근법:
- 다양한 라이브러리들을 사용하면 됨
- Nand2Tetris Part 2 에서 진행할 예정

Low-level 접근법:
- Bits.
- Nand2Tetris Part 1에서 진행

### Output

**Screen Memory map**
- display unit을 담당하기 위해 할당된 메모리 영역
- 실제 디스플레이는 초당 수차례 실제 메모리 맵에 맞춰서 refresh됨
- screen memory map을 변경하는 코드를 작성해서 output에 영향을 미칠 수 있음

### Screen memory map

display unit: (256 by 512, b/2)
- memory map: 8k 16 bits -> 120,000 pixels -> mapping screen memory map
- 어떤 bit이 어떤 pixel이 매핑되는지 정해야됨: bit = 1 dimension, pixel: 2 dimension
- 


