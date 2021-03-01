# Greedy algorithm

- 현재 상황에서 지금 당장 좋은것만 고르는 방법
- 문제를 풀기 위한 최소한의 아이디어
- 정당성 분석: 이 방법으로도 문제가 제공하는 최적의 해를 구할 수 있는지
  - 일반적인 상황에서 그리디 알고리즘으로는 최적의 해를 보장하지 못할 때가 많음


## 문제

### 거스름돈 문제 (500, 100, 50, 10)

**정당성 분석**

- 큰 단위가 항상 작은 단위의 배수이기 때문에
- 작은 단위의 배수가 아니라면 최적의 해를 보장해주지 못함 (i.e. 500, 400, 100...)

**시간복잡도**

- 화폐의 개수에 따라 증가하므로 `O(K)` (화폐가 K개일때)

```
n = 1260
count = 0

coins = [500, 100, 50, 10]

for coin in coins:
    count += n // coin
    n = n % coin
    
print(count)
```


### 나누기/빼기 문제

N이 1이 될 때까지 두 과정 중 하나를 반복적으로 수행
1. N에서 1을 뺌
2. N을 K로 나눔

N, K가 주어질 때 N이 1이 될 때까지 수행해야 하는 과정의 최소 횟수

```
N = 26
K = 4
count = 0

# n이 1일때까지 while문
# n 이 k로 나눠떨어지면 N //= K
# 아니라면 N -= K
while N != 1:
    if N % K == 0:
        N //= K
        print(N)
    else:
        N -= 1
        print(N)
    count += 1
```

답안

```
result = 0

# 시간복잡도를 log(n)으로 줄이기 위해서
while True:
    # k로 나누어 떨어지는 값 구해서
    target = (n // k) * k
    # 1을 빼는 연산회수를 한번에 계산
    result += (n - target)
    if n < k:
        break
    result += 1
    n //= k
    
# 마지막으로 남은 수에서 1 빼기 -> 왜?
result += (n - 1)
```

### 곱하기 or 더하기 문제

각 자리가 숫자 (0-9)로 이뤄진 문자열 S
왼쪽부터 오른쪽으로 하나씩 모든 숫자 사이에 x, +를 넣어 만들어질 수 있는 가장 큰 수

(단, 모든 연산은 왼쪽에서 순서대로 이루어진다고 가정. x 가 + 보다 먼저 되지 않음.)


```
# 0이 아니라면 무조건 곱하는게 크고, 0이라면 더하기

s = '02984'

result = 0

for i in s:
    num = int(i)
    # i가 0이거나 기존 값이 0일때는 무조건 더하기
    if num == 0 or result == 0:
        result += num
    # 아니라면 x
    else:
        result *= num
```

답안

```
# 두 숫자 중에 하나라도 '0', '1'인 경우 더해야됨

s = '02148'

result = int(s[0])
for i in range(1, len(s)):
    num = int(s[i])
    if num <= 1 or result <= 1:
        result += num
    else:
        result *= num
```

### 모험가 길드 문제

공포도가 X인 모험가는 반드시 X명 이상으로 구성한 모험가 그룹에 참가해야됨.
만들 수 있는 가장 많은 모험가 그룹은?

N = 5, 공포도=[2, 3, 1, 2, 2]

```
# 가장 작은 모험가부터 출발시키면 됨
# 정렬한 다음

n = 5
fear = [2, 3, 1, 2, 2]

result = 0
count = 0
fear.sort()

for i in fear:
    # 모인 모험가 수가 현재 모험가 공포도보다 크면 출발
    print(i)
    if i == 1:
        result += 1
    elif i <= count:
        result += 1 
    # 아니면 모험가 필요 count 증가
    else:
        count += 1
        print(result)
```

답안

```
n = 5
fear = [2, 3, 1, 2, 2]
fear.sort()

result = 0
count = 0

for i in fear:
    count += 1
    if count >= i:
        result += 1
        count = 0
```
