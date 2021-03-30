# 03. Memory

## 3.1. Sequential Logic

### Combinational Logic

- 지금까지는 시간에 대한 문제는 생각하지 않았음
- input이 있고, output은 그냥 intput의 function
  - 계산 이전에 다른 일들이 발생할 일이 없음
- "즉시" 계산됨

-> 이를 "Combinational Logic" 이라고 함

하지만, 실제 세상에서는 "time"을 고려해야함

### Hello, Time

크게 두가지를 고려해야됨

**같은 hardware를 사용함**
- input은 변하고 output은 따라와야됨
- ex. for loop

**State를 기억해야함**
- memory
- counters
- ex. for loop에서 sum을 계속 더할때

### The Clock

Physical time
- 실제 세상에서의 시간
- 컴퓨터 세상에서는 이런 복잡한 physical time을 clock이라는걸로 나눔

Clock
- 정해진 비율로 up/down (1/0)을 계속함
- 각 사이클은 한개의 digital integer time unit으로 취급
- physical time을 순차적인 digital integer time으로 나눔
- time unit에 따라서 실제 처리가 이뤄짐

![image](https://user-images.githubusercontent.com/23415251/112655328-9cfb8c80-8e93-11eb-899f-dd80003026c8.png)

> 이렇게 physical 시간을 integer time으로 쪼갬으로서 각각의 상태를 일정한 수준으로 유지할 수 있다 (stabilized)

- 각 상태 변화시에도 delay는 생기지만, 해당 delay가 포함된 interger time 이후에는 이전 state에 대해서는 생각할 필요 없음

### Combinational Logic vs Sequential Logic

- Combinational: `out[t] = function(in[t])`
- Sequential: `out[t] = function(in[t-1])`
  - remember previous output (new input)

![image](https://user-images.githubusercontent.com/23415251/112656332-a46f6580-8e94-11eb-8e0c-dd40eaf984b7.png)
![image](https://user-images.githubusercontent.com/23415251/112656347-a9341980-8e94-11eb-96e2-1557479a27d5.png)

-> Thinking in state
- Sequential: `state[t] = function(state[t-1])`

![image](https://user-images.githubusercontent.com/23415251/112656593-e5677a00-8e94-11eb-9166-8ea5b84d68a7.png)


## 3.2. Flip flops

### Remembering state

- Missing ingredient: time `t-1`의 1 bit를 time `t`에서 쓸 수 있도록 기억해야됨.
- `t-1`의 "end of time"에, 각각의 ingredient는 둘 중 하나: "remembering 0" or "remembering 1"
- 이 ingredient는 위 가능한 상태를 "flipping"을 통해 기억할 수 있음
- 두 상태간의 flip 할 수 있는 게이트를 "Flip-Flops"라고 부름

### The "Clocked Data Flip Flop"

- single input, single output
- 이전 input을 반환함: out[t] = in[t-1]

![image](https://i.imgur.com/3XTPMlU.png)


### Implementation of the D Fip Flop
- 실제 physical implementation에서는 Nand gate로 만들어짐
- 프로젝트에서는 combinational logic과 sequential logic 간의 혼동을 줄이기 위해서 이를 primitive한 chip으로 생각함

### Remembering forever: 1-bit register

- Goal: input bit을 "영원히" 기억하는것: 새로운 값이 로드되기 전까지

if load(t-1) then out(t) = int(t-1) else out(t) = out(t-1)

![image](https://i.imgur.com/03FCtWU.png)

![image](https://i.imgur.com/e2sk1pY.png)

## 3.3 Memory units

### Memory

메모리란?
- Main memory: RAM, ...
- Second memory: disks, ...
- Volatile / non-volatile (휘발성/비휘발성)으로도 구분 가능

RAM:
- Data
- Instruction

Perspective:
- Physical
- Logical

이 프로젝트에선 main memory, RAM, logical에 집중

### The most basic memory elkement: Register

이전에 만들었던 1-bit register를 이어서 multi-bit register를 만들 수 있음

multi-bit register는 `w` 크기의 input/output
- w (word width): 16-bit, 32-bit, 64-bit...
- 프로젝트에서는 16-bit register 사용
- Register's state: register에 현재 저장되어있는 값

### Register 

**read logic**

register의 값을 읽으려면?

`probe out`

-> register의 state를 반환해줌


**write logic**

```
set in = v
set load = 1
```

결과
- register의 state는 `v`
- 다음 사이클부터의 output은 `v`가 됨

### RAM unit

**RAM abstraction**


프로젝트에서 만들 칩은 16-bit RAM chips

![image](https://i.imgur.com/SHdLbmd.png)


## 3.4 Counters

### Where counters come to play

- 컴퓨터는 다음에 어떤 instruction이 fetch되어야하는지, execute 되어야하는지 알아야됨
- 3 control settings
  - Reset: fetch the first instruction (PC = 0)
  - Next: fetch the next instruction (PC++)
  - Goto: fetch instruction n (PC = n)

**Counter**
- A chip that realizes this abstraction

### Counter abstraction

- 16-bit in/out
- controller: load, inc, reset

```
if (reset[t] == 1) out[t+1] = 0           // resetting: counter = 0
else if (load(t) == 1) out[t+1] = in[t]   // setting counter = value
  else if (inc[t] == 1) out[t+1] = out[t] + 1 // incrementing: counter++
    else out[t+1] = out[t]
```

![image](https://i.imgur.com/5kxkKFL.png)


## 3.5 Project 3 Overview

**Given**
- All the chips built in project 1 and 2
- Data Flip-Flop (DFF gate)

**Goal**: building following chips

- Bit
- Register
- RAM8
- RAM64
- RAM512
- RAM4k
- RAM16k
- PC

### 1-bit register

```
if load then {
  Bit's state = in
  out emits the new state
}
else out emits the same value as that of the previous cycle
```

tips:
- Can be built from a DFF and a multiplexor

### 16-bit register

tips:
- Can be built from multiple 1-bit registers

### 8-Register RAM

![image](https://i.imgur.com/s8nTTjf.png)

### other rams

![image](https://i.imgur.com/hCbwBm9.png)

### Program Counter

tips:
- Can be built from a register, an incrementor, and some logic gates



