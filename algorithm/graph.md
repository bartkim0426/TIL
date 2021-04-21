# Graph

## 다익스트라 알고리즘

- 그리디 알고리즘: 매 상황에서 방문하지 않은 가장 비용이 적은 노드를 선택해 임의의 과정을 반복
- 한 번 처리된 노드의 최단 거리는 고정
    - 한 단계에 하나의 노드에 대한 최단거리를 확실히 찾는 것으로 이해

### 간단한 구현 방법

매 단계마다 방문하지 않은 노드 중에 최단거리가 가장 짧은 노드를 선택하기 위해 매 단계마다 1차원 테이블의 모든 원소를 순차탐색

이 경우 5000개가 넘어가면 보통 쓸 수 없음

### 우선순위 큐
- 우선순위가 높은 데이터를 가장 먼저 삭제
- heap을 사용
    - min heap, max heap이 있음
    - 삽입, 삭제에 모두 O(logN) -> 리스트를 사용하면 삽입은 O(1)이지만 삭제에 O(N)이 들기 때문

힙 사용 예: 최소 힙, 최대 힙

```
# 파이썬은 기본적으로 min heap
import heapq

def heapsort(iterable):
    h = []
    result = []
    for value in iterable:
        heapq.heappush(h, value)
        
    for i in range(len(h)):
        result.append(heapq.heappop(h))
    return result
    
# 최대 힙
# 별도로 제공하지 않기 때문에 데이터 부호를 바꿔서 넣고 빼야함

def heapsort(iterable):
    h = []
    result = []
    for value in iterable:
        heapq.heappush(h, -value)
        
    for i in range(len(h)):
        result.append(-heapq.heappop(h))
    return result
```

### 개선된 다익스트라
- 방문하지 않은 노드 중에 최단거리가 짧은 노드를 선택하기 위해 heap 사용
- 기본 원리는 동일
    - 현재 가장 가까운 노드를 저장하기 위해 힙 자료구조를 추가적으로 이용
    - 가장 짧은 노드를 선택해야하므로 최소 힙 사용

```
import heapq
import sys
INF = int(1e9)

# 노드 개수 n, 간선 개수 m
# n, m = map(int, input().split())
n = 5

# 시작 노드 번호
# start = int(input())
start = 1
# 각 노드 리스트
graph = [[] for i in range(n+1)]
# 최단거리 테이블 무한으로 초기화
distance = [INF] * (n+1)

# 간선 정보 입력
roads = [[1,2,1],[2,3,3],[5,2,2],[1,4,2],[5,3,1],[5,4,2]]	
for road in roads:
    a, b, c = road
    graph[a].append((b, c))

'''
아래 형태의 그래프가 나옴
[[],
 [(2, 1), (4, 2)],
 [(3, 3)],
 [],
 [],
 [(2, 2), (3, 1), (4, 2)],
 [],
 [],
 [],
 [],
 []]
'''

def dijkstra(start):
    q = []
    # 시작 노드의 최단경로를 0으로 설정하여 삽입
    heapq.heappush(q, (0, start))
    while q:
        # 가장 최단거리가 짧은 노드 정보 꺼내기
        current_distance, current_node = heapq.heappop(q)
        # 현재 노드가 이미 처리된 노드라면 무시: 꺼낸 값이 더 작다면 이미 처리된것으로 간주
        if distance[current_node] < current_distance:
            continue
        # 현재 노드와 연결된 인접 노드 확인
        for node in graph[current_node]:
            next_distance, next_node = node
            cost = current_distance + node[0]  # 시작 노드부터 현재 노드까지의 거리 + 다른 노드로 이동하는 거리
            # 현재 노드를 거쳐서 가는 거리가 더 짧은 경우 다시 push
            if cost < distance[node[1]]:
                distance[node[1]] = cost
                heapq.heappush(q, (cost, node[1]))
```

## 플로이드 워셜 알고리즘
- 다이나믹 프로그래밍 유형에 속함
