# Sorting

정렬 (Sorting)이란 "데이터를 특정한 기준에 따라 순서대로 나열하는 것"
- 문제 상황, 데이터 형태에 따라 적절한 알고리즘이 공식처럼 사용됨


## 선택 정렬 (Selection sorting)

- 처리되지 않은 데이터 중 가장 작은 데이터를 선택해 맨 앞에 있는 데이터와 바꾸는것

```
array = [7, 5, 9, 0, 3, 1, 6, 2, 4, 8]

for i in range(len(array)):
    # 처음에 시작했을떄 가장 작은 값은 해당 인덱스
    min_index = i
    # 해당 인덱스의 다음 값부터 끝까지 항상 순회
    for j in range(i+1, len(array)):
        # 다음 값이 가장 작은 값보다 작다면 min_index 업데이트
        if array[j] < array[min_index]:
            min_index = j
    array[i], array[min_index] = array[min_index], array[i]            
```

## 삽입 정렬 (insertion sorting)

- 처리되지 않은 데이터를 하나씩 골라 적절한 위치에 삽입
- 선택 정렬에 비해 일반적으로 효율적으로 동작

```
array = [7, 5, 9, 0, 3, 1, 6, 2, 4, 8]

# array[0]은 볼필요 없음
for i in range(1, len(array)):
    # 현재 검색하는 인덱스의 시점부터 처음까지 쭉 확인해야하니깐 i부터 1씩 줄여가면서 1까지
    changed_index = i
    for j in range(i-1, -1, -1):
        # 이전 값보다 현재 검색하는 인덱스의 값이 작으면
        if array[j] > array[i]:
            changed_index = j
    # 제거?
    item = array.pop(i)
    array.insert(changed_index, item)
```

실제 코드

```
array = [7, 5, 9, 0, 3, 1, 6, 2, 4, 8]

for i in range(1, len(array)):
    for j in range(i, 0, -1):
        # 이전 값보다 작은 경우
        if array[j] < array[j-1]:
            # 옮겨줌
            array[j], array[j-1] = array[j-1], array[j]
        # 자기보다 작은 데이터를 만나면 즉시 멈춤
        else:
            break
```

- 시간복잡도: `O(n**2)`
- 현재 리스트의 데이터가 거의 정렬되어 있는 상태라면 매우 빠르게 동작
  - 최선의 경우 (모두 정렬되어 있는 경우) O(n)

## 퀵 정렬 (Quick sorting)

- 일반적으로 데이터 종류와 상관 없이 표준적으로 사용할 수 있는 알고리즘
- 기준 데이터를 설정하고, 그 기준보다 큰 데이터와 작은 데이터의 위치를 바꾸는 방법
- 병렬 정렬과 더불어 대부분의 프로그래밍 언어의 정렬 라이브리드 근간이 되는 알고리즘 (ex. python, java .. -> 하이브리드 정렬)
- 가장 기본적인 퀵 정렬은 첫번쨰 데이터를 기준 데이터 (Pivot)으로 설정해서 동작

```
array = [5, 7, 9, 0, 3, 1, 6, 2, 4, 8]
```
- 현재 pivot 값은 5 (array[0])
  - 왼쪽에서부터 5보다 큰 데이터를 선택: 7
  - 오른쪽에서부터 5보다 작은 데이터를 선택: 4
  - 두 데이터의 위치를 서로 변경
- 이를 계속 반복
- 마지막에 위치가 엇갈리는 경우 (두 숫자가 변경되는 경우) 작은 데이터와 피벗 값의 위치를 바꿔줌: 분할
  - 이렇게 분할되면 5 왼쪽에 있는 데이터는 5보다 작고,
  - 오른쪽에 있는 데이터는 5보다 크다
  - Divide (분할): 이렇게 피벗을 기준으로 데이터 묶음을 나누는 작업
- 왼쪽, 오른쪽 각각 데이터에 대해 마찬가지로 퀵 정렬 수행 (재귀적)

### 퀵 정렬이 빠른 이유
- 이상적인 경우 분할이 절반씩 일어난다면 O(nlogn)
- 평균적으로 O(nlogn), 최악의 경우 `O(n**2)`
  - 그래서 표준 라이브러리 등에서는 최악의 경우에도 nlogn을 보장하게 만든다.

```
array = [5, 7, 9, 0, 3, 1, 6, 2, 4, 8]

def quick_sort(array, start, end):
    # 원소가 한개 남으면 종료
    if start >= end:
        return
        
    # 첫번째를 피벗으로 둠
    pivot = start
    left = start + 1
    right = end
    while left <= right:
        # 피벗보다 큰 데이터를 찾을떄까지 반복
        while left <= end and array[left] <= array[pivot]:
            left += 1
        # 오른쪽에서부터 피벗보다 작은 데이터를 찾을때까지 반복
        while right > start and array[right] >= array[pivot]:
            right -= 1
            
        # 엇갈렸다면 작은 데이터, 피벗 교체
        if left > right:
            array[right], array[pivot] = array[pivot], array[right]
        # 아니라면 작은 데이터, 큰 데이터 교체
        else:
            array[left], array[right] = array[right], array[left]
            
    # 왼쪽, 오른쪽 부분에서 각각 정렬 수행
    quick_sort(array, start, right - 1)
    quick_sort(array, right+1, end)
```

파이썬의 장점을 살린 코드

```
def quick_sort(array):
    # 원소가 한개 이하면 그냥 반환
    if len(array) <= 1:
        return array
    pivot = array[0]
    tail = array[1:]
    
    left_side = [x for x in tail if x <= pivot]  # 왼쪽 원소를 하나씩 확인하며 왼쪽 위치에 넣어줌
    right_side = [x for x in tail if x > pivot]  # 왼쪽 원소를 하나씩 확인하며 왼쪽 위치에 넣어줌
    
    # 분할 이후 왼쪽, 오른쪽을 재귀로 호출하고 가운데에 pivot을 놓으면 됨
    return quick_sort(left_side) + [pivot] + quick_sort(right_side)
```

## 계수 정렬: Counting sort

- 특정 조건에 부합할때에만 사용 가능하지만 매우 빠르게 동작
  - 데이터 크기 범위가 제한되어 정수 형태로 표현할 수 있을 때 사용
- 데이터 개수가 N, 양수 최대값이 K 일때 최악의 경우에도 O(n+k)를 보장

```
7 5 9 0 3 1 6 2 9 1 4 8 0 5 2
```

step 0: 가장 작은 데이터부터 가장 큰 데이터까지 범위기 모두 담길 수 있도록 리스트 생성
- 해당 값을 index로 두고
- 값에는 count를 올려줌

![image](https://i.imgur.com/z3vg80B.png)


결과를 확인할 때에는 리스트의 첫번째 데이터부터 하나씩 그 값만큼 반복하여 인덱스 출력

-> 공간 복잡도가 높지만 조건만 맞으면 빠르게 동작

```
array = [7, 5, 9, 0, 3, 1, 6, 2, 9, 1, 4, 8, 0, 5, 2]

count = [0] * (max(array) + 1)  # 0 ~ 9까지 index를 가져야 하므로 9 + 1

# 각 데이터에 해당하는 인덱스 값 추가
for i in range(len(array)):
    count[array[i]] += 1
    
# 추가된 count를 바탕으로 정렬 리스트 
answer = []
for i, x in enumerate(count):
    answer.extend([i]*x)
```

### 복잡도분석
- 시간복잡도, 공간복잡도: O(n+k)
  - for문을 n번, k번 돌기 때문
- 때에 따라서 심각한 비효율성을 초래할 수 있음
  - 0, 999999 가 2개 존재하는 경우, 1000000개의 인덱스를 가진 리스트를 만들어야됨
- 동일한 값을 가지는 데이터가 여러개 등장할 때 효과적
  - 성적의 경우 100점을 맞은 학생이 여러명일 수 있기 때문에 효과적


## 정렬 알고리즘 비교

![image](https://i.imgur.com/J4wFV27.png)
