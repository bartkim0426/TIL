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
- 16384부터 시작하는 총 8200개의 16bit register로 구성

(row, col) 의 스크린을 키려면
- word = Screen[23*row + col/16] = RAM[16384 + 32*row + col/16]
- word의 (col % 16)th번째 bit를 0에서 1로 만들어줌
- commit word to the RAM

![image](https://i.imgur.com/ZjXnVuM.png)

### Input

screen과 흡사하게 Keyboard memory map

- single 16 memory bit
- 현재 눌러지고 있는 키가 뭔지 체크
	- key가 입력되면 이미 정해진 스캔 코드(바이너리)가 키보드 메모리 맵에 나타남
	- Hack computer: RAM[24576] 사용

아래는 hack computer에서 사용할 셋

![image](https://i.imgur.com/XGRVwiY.png)

## 4.6. Hack Programming: Part 1

Hack Programming은 다음 일들을 함
- working with register and memory
- branching
- variables
- iterations
- pointers
- input/output

4.6장에서는 working with register and memory를 다룸

### Working with register and memory

- D: data register
- A: address/data register (상황에 따라)
- M: the currently selected memory register, M = RAM[A]

typical operations:

```
// D = 10
// 절대 direct로 할 수 없음
@10  // set A register to 10
D=A  // set D register to A (10)

// D++
D=D+1

// D=RAM[17]
@17  // set A register to 17 -> M register automatically set to RAM[17]
D=M

// RAM[17]=0
@17  // set A register to 17 -> M registert set to RAM[17]
M=0

// RAM[17] = 10
@10  // A register to 10, M register is RAM[10]
D=A  // D register to 10
@17 // set A register to 17, M register is RAM[17]
M=D

// RAM[5] = RAM[3]
@3  // A register to 3, M register is RAM[3]
D=M // D register = RAM[3]
@5  // A register to 5, M register is RAM[5]
M=D // M (RAM[5]) = D (RAM[3])
```

### example: Add two numbers

```
/// Program: Add2.asm
// Computes: RAM[2] = RAM[0] + RAM[1]
// Usage: put values in RAM[0], RAM[1]

@0    // M = RAM[0]
D=M   // D register = RAM[0]

@1    // M = RAM[1]
D=D+M // D = D(RAM[0]) + M(RAM[1])

@2    // M register = RAM[2]
M=D   // RAM[2] = D (RAM[0] + RAM[1])
```

when tranlate and load to ROM
- white space is ignored
- symbolic view -> binary view

### How to terminate a program properly?

따로 명령어를 입력하지 않으면 명령어가 없어도 프로그램이 계속됨
- infinite loop를 만들 수 있음
- 이후에 의도하지 않거나 malicious한 명령어를 실행시키지 않을 수 있음

```
...
@0
D=M
@1
D=D+M
@2
M=D
@6
0;JMP
```

### Built-in symbols

#### Virtual registers
R0, R1 .. R15
- 0 ~ 15
- "virtual registers"

```
@15   // A (data register)
D=A

@5    // A (address register)
M=D
```

이렇게 사용했을 때 문제
- 완전 다른 내용임에도 불구하고 (data, address) 같은 코드 (@)를 사용
- A instruction만 읽었을 때 무슨 역할을 하는지 알 수 없음

이를 해결하기 위해 첫 16 register를 읽을 때에는 label convention을 사용

```
@15
D=A

@R5   // much more readable
M=D
```

> Hack is case-sensitive: R5와 r5는 다른 symbol. 대부분의 machine language는 case-sensitive 임.


#### Screen, keyboard

- SCREEN: 16384
- KBD: 24576

그리고 이번 코스에서 사용하지 않는 6가지 symbol이 있음


정리하면

- R0, R1, ..R15: "virtual registers"
- `SCREEN`, `KBD`: base address of I/O memory maps
- SP, LCL, ARG, THIS, THAT: part 2에서 사용


## 4.7. Hack Programing, Part 2

- working with register and memory
- branching
- variables
- iterations
- pointers
- input/output

저번 장에서는 working with register and memory를 다룸. 이번 장에서는 branching과 variables

### Branching

machine language에서는 보통 한가지의 branching만 가짐. `GO to`

예시: R0이 0일 경우에 R1에 1을 할당하고 아니면 0을 할당 (특정 값이 0인지 아닌지 확인하는 프로그램)
- low level programming에서는 이를 바로 표현할 수 없음
- 이를 위해 goto를 사용

(아래 예시의 옆에 번호 0, 1,..는 실제로 ROM이 읽는 line number이다)
```
// Program: Signum.asm
// Computes: if R0>0
//              R1=1
//           else
//              R1=0
0  @R0    // A, M = RAM[0]
1  D=M    // D = RAM[0]

2  @8
3  D;JGT  // IF D > 0 goto 8

4  @R1
5  M=0    // RAM[1] = 0
6  @10
7  0;JMP  // jump to 10 (end of program)

8  @R1
9  M=1    // RAM[1] = 1

10 @10
11 0;JMP  // infinite loop to end program
```

위 코드는 잘 동작하지만 readable 하지 않음

> Instead of imagining that our main task is to instruct a computer what to do, let us concentrate rather on explaining to human beings what we want a computer to do.

assembly language에는 "symbolic reference"가 있음
- 해당 줄을 직접 명시하는 대신 label를 지정하고 사용할 수 있다
- declaring a label
- use a label


```
@R0  
D=M  

@POSITIVE  // using a label
D;JGT

@R1
M=0  
@END        // using a label
0;JMP

(POSITIVE)  // declaring a label
@R1
M=1  

(END)
@END
0;JMP
```

실제 label instruction은 ROM에 올라갈 때 무시됨
- label declaratinos은 translate 되지 않음
- label reference는 해당 label 다음에 나오는 줄로 변환됨

![image](https://i.imgur.com/mgWAIi2.png)


### Variables

variable usage example:

```
// program: Flip.asm
// flips the values of
// RAM[0] and RAM[1]

// temp = R1
// R1 = R0
// R0 = temp

@R1    // A, M == RAM[1]
D=M    // D = RAM[1]
@temp  // declare variable (M = temp)
M=D    // temp = R1

@R0    // M == RAM[0]
D=M    // D = RAM[0]
@R1    // M = RAM[1]
M=D    // M (R1) = RAM[0]

@temp  // declare variable (M = temp)
D=M    // D = temp
@R0    // M = RAM[0]
M=D    // M (R0) = temp (R1)

(END)
@END
0;JMP
```

위 machine language가 실제로 ROM에서는 다음과 같이 읽힘. `@temp`가 `@16`이 된 것을 볼 수 있다.

```
@1
@D=M
@16
M=D
```

`@temp`의 뜻은?
- "find some available memory register (say register n), and use it to represent the variable `temp`."
- 그때무너 프로그램의 `@temp`는 모두 `@n`이 된다.

Contract:
- 특별한 라벨이 정의되어 있지 않은 symbol은 variable로 간주
- varibale은 RAM에 16부터 정의됨. 더 많은 varibale이 생기면 17, 18... 로 할당됨


### Iteration

example: compute 1 + 2 + ... + n

- pseudocode 먼저 작성


```
// Computes RAM[1] = 1+2+...+RAM[0]
n = R0
i = 1
sum = 0
LOOP:
  if i > n goto STOP
  sum = sum + i
  i = i + 1
  goto LOOP
STOP
  R1 = sum
```

이를 machine language로 구현해보면 다음과 같음

```
// Program: Sum1toN.asm
// Computes RAM[1] = 1+2+...+n
// Usage: put a number(n) in RAM[0]

@R0  // M = RAM[0]
D=M  // D = RAM[0]
@n   // M = n
M=D  // n = R0 (RAM[0]
@i   // M = i
M=1  // i = 1
@sum // M = sum
M=0  // sum = 0

(LOOP)
@i   // M = i
D=M  // D = i
@n   // M = n
D=D-M // D = D(i) - M(n) = i - n
@STOP
D;JGT  // Goto STOP if D > 0 => i-n > 0 => i > n

@sum  // M = sum
D=M   // D = sum
@i    // M = i
D=D+M // D = D(sum) + M(i) =-> sum + i
@sum  // M = sum
M=D   // M(sum) = sum + i
@i    // M = i
M=M+1 // i = i + 1
@LOOP
0;JMP // Goto Loop (unconditionally)

(STOP)
@sum  // M = sum
D=M   // D = sum
@R1   // M = RAM[1]
M=D   // M(RAM[1]) = sum

(END)
@END
0;JMP
```

이 상태로 ROM에 올리면 각각의 변수 (n, i, sum)는 @16, @17, @18로 정의됨



Best practice:
- 1. 코드를 작성할 떄에는 pseudocode로 먼저 작성
- 2. assembly language로 해당 프로그램 작성
- 3. 작성한 이후에 종이에 직접 적어보면서 실제로 작동하는지 확인해보는게 좋음 (variable-value trace table)

![image](https://i.imgur.com/ARost52.png)


## 4.7 Hack Programming, Part 3

- working with register and memory
- branching
- variables
- iterations
- pointers
- input/output

이번 장에서는 pointers, input/output

### Pointers

example.

high level language와는 다른 방식.

machine language에서 array는 그저 해당 영역의 메모리를 나타낼뿐

```
// for (i=0; i<n; i++) {
//     arr[i] = -1
// }

// suppose arr=100, n=10

// arr=100
@100  // A = 100
D=A   // D = 100
@arr  // M = arr
M=D   // arr=D (100)

// n=10
@10   // A = 10
D=A   // D=10
@n    // M=n
M=D   // n=10

// initialize i = 0
@i    // M = i
M=0   // i = 0
...
```

위 코드대로면 RAM[16] = 100, RAM[17] = 10, RAM[18]=0 으로 할당된다.
(variable을 사용하면 RAM[16]부터 순차적으로 할당되기 때문)

```
...
(LOOP)
//  set termination
//  if (i==n) goto END

//  RAM[arr+i] = -1
@arr  // M=arr
D=M   // D=arr
@i    // M=i
A=D+M // A = arr+i
M=-1  // M = RAM[arr+i] = -1

// i++
@i    // M=i
M=M+1 // M=i+1

@LOOP
0;JMP // unconditionally jump to loop

(END)
@END
0;JMP
```

array를 만들기 위해 arr이 할당된 100 번째 ram부터 순차적으로 넣어줌

`A=D+M`
- 처음으로 A 레지스터에 직접 할당함
- D+M은 address를 저장함 (우리가 할당하려고 하는 `RAM[arr+i]`)
- `A=D+M` 명령어를 실행시키면 이 주소 (arr+i)가 A에 할당됨
- `M=-1`을 실행시키면 M이 가리키는 A address (RAM[arr+i])를 -1로 만들어준다.

**pointer**
- memory address를 저장한 변수 (arr, i)를 `pointers`라고 부른다
- Hack pointer logic: pointer를 사용해서 메모리에 접근할 때마다 `A=M`같은 instruction을 사용
- "Set the address register to the contents of some memory register"

![image](https://i.imgur.com/hJ1Nhk5.png)


### Input/Output

- screen: 16384 th register
  - 8K bit
  - base address of the screen memory map
  - 위 8k register들의 값에 따라서 스크린에 표시 (1/0)
- keyboard: 24576 th register
  - address of the keyboard memory map
  - 실제로 눌러지고 있는 키보드의 값을 해당 레지스터에 등록 (입력값이 없으면 0)

#### example: drawing a rectangle to screen

- 16 pixel wide를 가지고, RAM[0] pixel 길이(height)를 가진 rectangle을 그리는 태스크

rectangle.asm

#### Rectangle drawing: pseudo code

```
// for (i=0; i<n; i++) {
//     draw 16 black pixels at the beginning of row i
// }

// set variables
addr = SCREEN
n = RAM[0]
i = 0

LOOP:
  if i > n goto END
  RAM[addr] = -1   // 11111111111111
  // advances to the next row
  addr = addr + 32  // 16*32 = 512 (512 pixel), go next line with add 33
  i = i + 1
  goto LOOP

END:
  goto END
```


#### Rectangle drawing: machine language

```
@R0
D=M
@n
M=D   // n = RAM[0]

@i
M=0   // i = 0

@SCREEN
D=A
@addr
M=D   // address = 16384 (base address of the Hack screen)

(LOOP)  // if i>n goto END
@i    // M = i
D=M   // D = i
@n    // M = n
D=D-M // D = i-n
D;JGT // if D > 0 -> i-n > 0 -> i > n goto END

@addr // M = addr
A=M   // M = RAM[addr]
M=-1  // M = RAM[addr] = -1 = 11111111111111

@i    // M = i
M=M+1 // i = i + 1
@32   // A = 32
D=A   // D = 32
@addr // M = addr
M=D+M // addr = 32 + addr : next line
@LOOP
0;JMP // unconditionally goto LOOP

(END)
@END
0;JMP
```

#### Working with the keyboard


keyboard가 눌러지면 해당 키의 "scan code"가 할당된 keyboard memory register (24576)에 할당된다.

키보드가 현재 눌러져있는지 확인하려면
- Read the content of RAM[24576] (address `KBD`)
- register가 0이라면 키가 눌러지지 않음
- 0이 아니라면 레지스터에 들어있는 값이 현재 눌러지고 있는 key의 scan code

"Simple people are impressed by sophisticated things, and sophisticated people are impressed by somple things."


### 4.9. Project 4 Overview

project objectives
- low level programming
- Hack assembly language
- Hack hardware

Tasks:
- Write a simple algebraic program
- Write a simple interactive program


#### 1. Mult: a program performming `R2 = R0 * R1`

`RAM[0] * RAM[1]`의 값을 `RAM[2]`에 저장하는 프로그램

hint
- 곱하기가 제공되지 않기 때문에 더하기, 빼기 등 사용
- loop 사용

#### 2. a simple interactive program

- interaction with keyboard
- key가 눌러지면 black screen이 됨
- key가 떼지면 다시 white screen

2 seperated challenge
- keyboard가 눌러졌는지 아닌지
- screen을 키고 끄는것

Implementation strategy
- listen to the keyboard
- To blaken/clear the screen, write code that fills the entire screen memory map
- Addressing the memory requires working with pointers


#### Program development process

- write/edit the program: `Prog.asm`
- Load program into CPU Emulator and run it

#### Best practice

Well-written low-level code is:
- Short
- Efficient
- Elegant
- Self-describing

Technical tips:
- Use symbolic variables and labels
- Use sensible variable and label names
- Variables: lower-case (convention)
- Labels: upper-case (convention)
- Use indentation
- Start with pseudo code


아래는 작성한 코드이다

`mult.asm`

```
// pseudo code
// // set variable
// a = RAM[0]
// b = RAM[1]
// c = RAM[2]
// i = 0
// sum = 0
// 
// LOOP:  // a + a + .. + a -> b times
//     if i >= b goto END
//     sum = sum + a
//     i = i + 1
//     c = sum
//     goto LOOP
// 
// END:
//     goto END

@R0   // M = RAM[0]
D=M   // a = RAM[0]
@a    // M = a
M=D   // a = RAM[0]

@R1   // M = RAM[1]
D=M   // b = RAM[1]
@b    // M = b
M=D   // b = RAM[1]

@i   // M = i
M=0  // i = 0
@sum // M = sum
M=0  // sum = 0

(LOOP)
    // if i >= b goto END
    // i >= b -> i-b >= 0
    @b    // M = b
    D=M   // D = b
    @i    // M = i
    D=M-D // D=i-b
    @END
    D;JGE // if i - b > 0 goto END

    // i = i + 1
    @i     // M = i
    M=M+1  // i = i + 1

    // sum = sum + a
    @a    // M = a
    D=M   // D = a
    @sum  // M = sum
    M=M+D // sum = sum + a

    // RAM[2] = sum
    D=M   // D = sum
    @R2   // M = RAM[2]
    M=D   // RAM[2] = sum

    // unconditionally goto LOOP
    @LOOP
    0;JMP

(END)
@END
0;JMP
```

`fill.asm`

```
// pseudo code
// (RESET)
// addr = SCREEN
// kbd = KBD
// 
// (KBDCHECK)
// if kbd > 0 goto BLACK
// if kbd == 0 goto WHITE
// 
// WHITE:
//     filled = 0
//     goto CHANGE
// 
// BLACK:
//     filled = -1
//     goto CHANGE
// 
// CHANGE:
//     addr = filled
//     addr = addr + 1
//     d = kbd - screen
// 
//     if d = 0 goto RESET
//     goto CHANGE

(RESET)
    @SCREEN       // M = screen (RAM[16384]), A = 16384
    D=A           // D = 16384
    @screen_count // M = screen_count
    M=D           // screen_count = 16384


(KBC_CHECK)
    @KBD          // M = KBD (RAM[24576]), A = 24576
    D=M           // D = KBD
    @BLACK
    D;JGT         // if KBD > 0 goto BLACK
    @WHITE
    D;JEQ         // if KBD == 0 goto WHITE

    @KBD_CHECK
    0;JMP         // infinitly check keyboard


(BLACK)
    @filled       // M = filled
    M=-1          // filled = -1
    @CHANGE_SCREEN
    0;JMP         // unconditionally goto CHANGE_SCREEN


(WHITE)
    @filled       // M = filled
    M=0           // filled = 0
    @CHANGE_SCREEN
    0;JMP         // unconditionally goto CHANGE_SCREEN


(CHANGE_SCREEN)
    @filled       // M = filled
    D=M           // D = filled (-1 for BLACK, 0 for WHITE)

    @screen_count // M = screen_count, A = 16384 (address)
    A=M           // A =16384
    M=D           // RAM[16384] = filled (-1 or 0)

    @screen_count // M = screen_count (RAM[0])
    D=M+1         // D = 16384 + 1

    @KBD          // A = 24576
    D=A-D         // D = 24576 - (16384 + 0)
                  // D = 8191, 8190, 8189, ... 0

    @screen_count // M = screen_count
    M=M+1         // M = RAM[0] + 1 = 16384 + 1

    @RESET        // if D == 0 goto RESET
    D;JEQ

    @CHANGE_SCREEN       // unconditionally LOOP in CHANGE
    0;JMP
```
