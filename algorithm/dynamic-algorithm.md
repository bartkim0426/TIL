# dynamic algorithm

다이나믹 프로그래밍: 메모리를 적절히 사용하여 수행 시간 효율성을 비약적으로 향상시키는 방법
- 이미 계산된 결과(작은 문제)는 별도의 메모리 영역에 저장하여 다시 계산하지 않도록 함
- 구현은 일반적으로 top-down, bottom-up 두가지로 구성
- 최적 부분 구조: Optimal Substructure
  - 큰 문제를 작은 문제로 나눌 수 있으며 작은 문제를 모아서 답을 낼 수 있음
- 중복되는 부분 문제: Overlapping Subproblem
  - 동일한 작은 문제를 반복적으로 해결


ex. 피보나치 수열 문제

1, 1, 2, 3, 5, 8, 13, 21 ...

- 인접한 항들 사이의 관계식인 '점화식'으로 표현해서 해결 가능
- a[n] = a[n-1] + a[n-2], a[0] = 1, a[1] = 1

```
# 일반적인 풀이

answer = [1, 1]
x = 10  # 10번쨰 값 구하기
for i in range(2, x):
    next_answer = answer[i-1] + answer[i-2]
    answer.append(next_answer)

# 재귀함수 풀이
def fibonacci(x):
    if x == 1 or x == 2:
        return 1
    return fibonacci(x-1) + fibonacci(x-2)
```

재귀로 사용해서 풀면 지수 시간 복잡도 `O(2**n)`

트리로 확인해보면 동일한 부분이 여러번 호출되는걸 볼 수 있음 (ex. f(5) -> f(4), f(3). f(4) -> f(3), f(2) ...)

두가지 방법

### bottom-up

top-down 방식에서 사용하는 방식: Memorization
- 한 번 계산한 결과를 메모리 공간에 메모하는 기법
  - 같은 문제를 다시 호출하면 메모했던 결과를 가져옴
  - 이를 Caching이라고 표현하기도 함

전형적인 형태는 bottom-up 방식: 반복문

```
# memorization

# memorization 하기 위한 리스트 초기화. 100으로 설정하면 99까지 계산 가능
cache = [0] * 100

def fibonacci(x):
    if x == 1 or x == 2:
        return 1
    # 이미 계산한 적 있는 문제라면 그대로 반환
    if cache[x] != 0:
        return cache[x]
    # 아직 계산하지 않은 문제라면 계산
    cache[x] = fibonacci(x-1) + fibonacci(x-2)
    return cache[x]
```

```
# bottom-up 방식

cache = [0] * 100
cache[1], cache[2] = 1, 1
n = 99

for i in range(3, n+1):
    cache[i] = cache[i-1] + cache[i-2]
print(cache[n])
```

이렇게 만든 경우 시간복잡도는 O(n)


### 다이나믹 프로그래밍 접근법

주어진 문제가 다이나믹 프로그래밍 유형임을 파악하는게 가장 중요
- 그리디, 구현, 완전탐색 등의 아이디어로 해결할 수 있는지 검토
  - 다른 알고리즘 풀이 방법이 떠오르지 않는다면 다이나믹 프로그래밍을 고려
- 일단 재귀 함수로 비효율적인 완전 탐색 프로그램을 작성한 뒤에 작은 문제에서 구한 답이 큰 문제에 사용될 수 있다면 코드를 개선
