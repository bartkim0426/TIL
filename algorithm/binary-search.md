# Binary search

순차탐색: 특정 데이터를 찾기 위해서 앞에서부터 확인하는 방법
이진탐색: 탐색 범위를 절반씩 좁혀가며 데이터를 탐색하는 방법
- 시작점, 끝점, 중간점
- ex. 이미 정렬된 10개의 데이터 중 값이 4인 원소를 찾는 예시
  - 시작접: 0, 끝점: 9, 중간점 4 (index)
  - 중간점을 기준으로 다시 끝점, 중간점을 옮김

시간복잡도: logN (단계마다 탐색범위를 2로 나누는 것)


### 이진탐색 소스: 재귀적 구현

```python
def binary_search(array, target, start, end):
    if start > end:
        return None
    mid = (start + end) // 2
    # 찾은 경우 중간점 인덱스 반환
    if array[mid] == target:
        return mid
    # 중간점의 값보다 target이 작은 경우 왼쪽 확인
    # end를 mid - 1 index로
    elif array[mid] > target:
        return binary_search(array, target, start, mid - 1)
    # 중간점의 값보다 target이 큰 경우 오른쪽 확인
    # start를 mid+1 index로
    else:
        return binary_search(array, target, mid + 1, end)
```

### 이진탐색 소스: 반복문 구현

```python
def binary_search(array, target, start, end):
    while start <= end:
        mid = (start + end) // 2
        if array[mid] == target:
            return mid
            
        elif array[mid] > target:
            end = mid - 1
        else:
            start = mid + 1
    return None
```

### 파이썬 이진탐색 라이브러리

- `bisect_left(a, x)`
- `bisect_right(a, x)`

값이 특정 범위에 속하는 데이터 개수 구하기

```python
def count_by_range(a, leeft_value, right_value):
    right_index = bisect_right(a, right_value)
    left_index = bisect_left(a, left_value)
    return right_index - left_index
```

### 파라메트릭 서치

- 최적화 문제를 결정 문제로 바꾸어 해결하는 기법
- ex. 특정한 조건을 만족하는 가장 알맞은 값을 빠르게 찾는 문제
- 이런 문제는 이진탐색으로 해결할 수 있다


## 문제

### 떡볶이 떡 만들기 문제

떡 길이가 일정하지 않는데 한봉지에 들어가는 총 길이는 절단기로 맞춰서 잘라줌
- 절단기에 높이를 지정하면 줄지어진 떡을 한번에 절단
- 높이(h)보다 긴 떡은 잘리고 낮은 떡은 안잘림
- 손님이 요청한 길이가 M일 때 최소 M만큼의 떡을 얻기 위해 절단기에 설정할 수 있는 높이의 최댓값
- 절단기의 높이는 0부터 10억

**아이디어**
- 적절한 높이를 찾을때까지 이진탐색을 수행
- 조건 만족을 확인한 후에 조건 만족여부에 따라 탐색 범위를 좁히기
- 큰 탐색 범위(10억)를 보면 `이진탐색`을 떠올리면 됨


```python
n, m = 4, 6
array = [19, 15, 10, 17]

# 시작, 끝점
start = 0
end = max(array)

result = 0
while(start <= end):
    total = 0
    mid = (start + end) // 2
    for x in array:
        # 잘린 떡 계산
        if m > mid:
            total += x - mid
            
    # 떡이 부족한 경우 왼쪽부분 탐색 (더 많이)
    if total < m:
        end = mid - 1
    # 충분한 경우 오른쪽 부분 탐색 (더 적게)
    else:
        result = mid  # 충분한 경우 (덜 잘렸을 때) result 기록
        start = mid + 1
```


### 정렬된 배열에서 특정 수 개수 구하기

오름차수 수열 N에서 x가 등장하는 개수 계산
- 시간복잡도 O(logN)으로 설계하지 않으면 시간초과




