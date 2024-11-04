
힙(Heap) 자료구조
========================

**힙(Heap)** 은 **완전 이진 트리** 기반의 자료구조로, 우선순위 큐 구현에 많이 사용됩니다. 힙에는 **최대 힙(Max Heap)** 과 **최소 힙(Min Heap)** 두 가지 종류가 있으며, 각각 특정한 우선순위를 유지하는 방식으로 요소를 정렬합니다.

**힙의 종류**

 1. **최대 힙 (Max Heap)**
     각 부모 노드의 값이 자식 노드의 값보다 크거나 같은 구조입니다.
     트리의 루트 노드에는 항상 가장 큰 값이 위치합니다.
     예를 들어, 루트 노드를 삭제하거나 가장 큰 값을 얻어야 할 때 유용합니다.

 2. **최소 힙 (Min Heap)**
     각 부모 노드의 값이 자식 노드의 값보다 작거나 같은 구조입니다.
     트리의 루트 노드에는 항상 가장 작은 값이 위치합니다.
     예를 들어, 최솟값을 빠르게 얻어야 할 때 유용합니다.

**힙의 특징**

 * **완전 이진 트리**: 힙은 트리의 모든 레벨이 완전히 채워지며, 마지막 레벨은 왼쪽부터 채워지는 완전 이진 트리 구조입니다.
 * **우선순위 정렬**: 힙은 루트에서 우선순위를 보장하여 삽입과 삭제가 이루어집니다.
 * **힙 성질 유지**: 힙 구조는 삽입, 삭제 시마다 힙 성질을 유지하기 위해 재정렬(Heapify) 과정을 거칩니다.

**힙의 주요 연산**

   1. **삽입 (Insert)**
       새로운 값을 힙의 가장 마지막 위치에 삽입하고, 상향식 재정렬 (Up-Heapify) 과정을 통해 힙 속성을 유지합니다.
       상향식 재정렬에서는 삽입된 노드와 부모 노드를 비교하며 필요할 경우 위치를 교환합니다.

   2. **삭제 (Delete)**
       루트 노드를 제거하고, 마지막 노드를 루트로 이동시킨 후 하향식 재정렬 (Down-Heapify) 과정을 통해 힙 속성을 복구합니다.
       하향식 재정렬에서는 루트 노드와 자식 노드를 비교하며 필요한 경우 위치를 교환합니다.

   3. **최대값 또는 최소값 얻기 (Get Max/Min)**
       최대 힙에서는 루트 노드에 최대값이, 최소 힙에서는 루트 노드에 최소값이 저장되어 있으므로 **O(1)**의 시간 복잡도로 접근할 수 있습니다.

**힙의 구현 방식**

힙은 주로 배열로 구현됩니다. 배열에서 인덱스 기반으로 부모와 자식 간의 관계를 유지하는 방식으로 간편하게 구현할 수 있습니다.

    부모 노드 인덱스 i의 왼쪽 자식 노드 인덱스는 2i+1
    부모 노드 인덱스 i의 오른쪽 자식 노드 인덱스는 2i+2
    자식 노드 인덱스 i의 부모 노드 인덱스는 i−1/2 (소수점 버림)

**힙의 시간 복잡도**

   * **삽입과 삭제**: **O(log⁡n)** — 힙의 높이가 **log⁡n**이므로, 힙 성질을 유지하기 위해 필요한 재정렬 과정의 시간 복잡도가 **O(log⁡n)** 입니다.
   * **최대값 또는 최소값 얻기** : **O(1)** — 루트 노드에 최대값 또는 최소값이 저장되어 있어, 접근이 빠릅니다.

**힙의 높이와 시간 복잡도**

   * **힙의 높이 (Height):**
       힙이 완전 이진 트리라는 점에서 힙에 포함된 **노드의 수 n**에 따라 높이가 **O(log⁡n)** 로 증가합니다.
       예를 들어, 노드가 1개일 때는 높이가 0, 노드가 2개일 때는 높이가 1, 노드가 4개일 때는 높이가 2가 됩니다.
       노드가 두 배로 늘어날 때마다 높이는 한 단계씩 증가하므로, 노드의 수 nn과 트리의 높이 h는 h=log⁡2n 관계에 있습니다. 여기서, 로그의 밑은 보통 생략하여 **O(log⁡n)** 로 표기합니다.

   * **삽입 연산**:
       새로운 노드를 삽입할 때는, 트리의 가장 마지막 위치에 노드를 추가하고, 힙의 성질(부모 노드가 자식 노드보다 크거나 작다는 성질)을 유지하기 위해 상향식 재정렬 (**Up-Heapify**) 과정을 수행합니다.
       이 과정에서 새로 삽입된 노드는 부모 노드와 비교하면서 최종 위치에 도달할 때까지 위로 이동하게 됩니다. 최악의 경우 트리의 루트까지 이동해야 하므로 트리의 높이만큼 연산이 필요합니다.
       따라서, 삽입 연산의 시간 복잡도는 트리의 높이에 비례하여 **O(log⁡n)** 입니다.

   * **삭제 연산**:
       힙에서 노드를 삭제할 때는 보통 루트 노드를 삭제합니다(최대 힙에서는 최대값, 최소 힙에서는 최소값).
       루트 노드를 삭제한 후, 마지막 노드를 루트로 이동시키고, 힙의 성질을 유지하기 위해 하향식 재정렬 (**Down-Heapify**) 과정을 수행합니다.
       이 과정에서는 루트에서 시작하여 자식 노드들과 비교하며 자리를 이동합니다. 이 역시 최악의 경우 트리의 맨 아래까지 이동해야 하므로 트리의 높이에 비례하는 연산이 필요합니다.
       따라서, 삭제 연산의 시간 복잡도도 **O(log⁡n)** 입니다.

**힙의 활용 예시**

   * **우선순위 큐** : 대기열에서 우선순위에 따라 작업을 처리해야 할 때 사용됩니다. 예를 들어, CPU 스케줄링, 네트워크 패킷 관리 등에서 유용합니다.
   * **힙 정렬**: 힙을 사용한 정렬 알고리즘으로, 최대 힙 또는 최소 힙을 사용하여 배열을 정렬합니다. 최악의 경우에도 O(nlog⁡n)복잡도로 수행됩니다.
   * **그래프 알고리즘** : 다익스트라 알고리즘, 프림 알고리즘 등에서 최소 힙을 사용하여 최단 경로 및 최소 신장 트리를 효율적으로 구할 수 있습니다.

    
완전이진트리
=============
완전 이진 트리는 **이진 트리(Binary Tree)** 의 한 종류로, 모든 레벨이 꽉 차 있으며, 마지막 레벨은 왼쪽부터 채워지는 트리를 말합니다. 
완전 이진 트리는 특정한 규칙을 갖고 있어 다양한 자료구조와 알고리즘에서 효율적인 구조로 사용됩니다.
완전 이진 트리의 특징

   1. **모든 레벨이 완전히 채워짐**:
       트리의 모든 레벨(**마지막 레벨 제외**) 이 노드로 가득 차 있습니다.
       **마지막 레벨**만 비어 있을 수 있으며, 그 경우 노드는 왼쪽부터 채워집니다.

   2. **왼쪽부터 채워짐**:
       마지막 레벨에 노드가 추가될 때는 왼쪽부터 오른쪽으로 순서대로 채워집니다. 이 규칙이 있어 트리가 항상 균형에 가깝게 유지됩니다.

   3. **높이 최소화**:
       노드 개수가 주어졌을 때, 완전 이진 트리는 최소 높이를 갖습니다. 높이가 최소화되므로 탐색과 삽입, 삭제가 효율적입니다.

```markdown
        1
      /   \
     2     3
    / \   / \
   4   5 6   7
```

다음과 같은 트리도 완전 이진 트리입니다:
```markdown
        1
      /   \
     2     3
    / \   / 
   4   5 6   

```
위 트리에서 마지막 레벨(높이 2)은 왼쪽부터 노드가 채워져 있습니다.

HeapQ test
==================

```python
import heapq

# 빈 힙 생성
heap = []

# 힙에 요소 추가
heapq.heappush(heap, 5)
heapq.heappush(heap, 1)
heapq.heappush(heap, 3)

# 힙에서 가장 작은 요소 제거 및 반환
smallest = heapq.heappop(heap)  # smallest는 1이 됩니다.

# 힙 상태 출력
print(heap)  # [3, 5]
```



A* with Heuristic
==================

A* 알고리즘에서 **휴리스틱 함수 (Heuristic Function)** 는 현재 노드에서 목표 노드까지의 예상 비용을 추정하는 데 사용됩니다. 
이 함수는 알고리즘이 더 효율적으로 목표에 도달하도록 도와주며, 길찾기 문제에서 최적의 경로를 찾는 데 중요한 역할을 합니다.

A* 알고리즘은 경로의 **총 비용 f(n)** 을 최소화하는 방식으로 작동합니다. 여기서 f(n)은 다음과 같이 정의됩니다:

  f(n)=g(n)+h(n)
  ---------------
g(n): 시작 노드에서 현재 노드 n까지 도달하는 실제 비용입니다.
h(n): 현재 노드 n에서 목표 노드까지의 예상 비용(휴리스틱 값)입니다.

휴리스틱 함수 h(n)가 어떻게 정의되느냐에 따라 A 알고리즘의 효율성과 정확성이 달라집니다. 보통 다음과 같은 조건을 만족해야 합니다:

 1. **비관적인 추정 (Admissible)**: **휴리스틱 함수 h(n)은 실제 비용을 초과하지 않아야 합니다.**
    즉, 목표까지의 비용이 아무리 작아도 h(n))이 그보다 작거나 같아야 합니다. 이렇게 하면 A* 알고리즘이 항상 최적의 해를 찾을 수 있습니다.

 2. **일관성 (Consistent 또는 Monotonic)**: 모든 노드 n과 그 인접 노드 m에 대해, 다음 조건이 성립해야 합니다.
    **h(n) ≤ c(n,m)+h(m)**


여기서 c(n,m)은 n에서 m으로 이동하는 실제 비용입니다. 
일관성을 만족하는 휴리스틱 함수는 비관적인 특성을 자동으로 만족하게 되며, A* 알고리즘이 효율적으로 동작하게 해줍니다.**


A* 알고리즘의 작동 방식
----------------------

1. **초기화**: 출발 노드를 시작 지점으로 설정하고, 그 노드를 **열린 목록 (Open List)**에 추가합니다. 열린 목록에는 탐색할 노드들이 포함됩니다.

2. **노드 선택**: 열린 목록에서 f(n) 값이 가장 작은 노드를 선택하여 현재 노드로 설정합니다.

3. **목표 도달 여부 확인**: 현재 노드가 목표 지점인 경우, 탐색을 종료하고 경로를 반환합니다.

4. **이웃 노드 탐색**: 현재 노드의 모든 인접 노드(이웃 노드)를 확인합니다.
    각 이웃 노드에 대해 f(n)=g(n)+h(n) 값을 계산합니다.
    이 값이 기존에 계산된 값보다 작으면 해당 노드의 f(n), g(n), h(n) 값을 업데이트하고, 열린 목록에 추가합니다.

5. **반복**: 목표 지점에 도달하거나 열린 목록이 빌 때까지 위의 과정을 반복합니다.

6. **경로 반환**: 목표 지점에 도달한 경우, 탐색 과정을 통해 최단 경로를 추적하여 반환합니다.

    

```python
# -----------
# Problem:
#
# A* 탐색 알고리즘을 사용하여 주어진 grid에서 목표 위치까지의 최적 경로를 찾고,
# 각 셀의 확장 순서를 포함한 expand 그리드를 반환하는 search() 함수입니다.
# 확장되지 않은 셀은 -1로 표시됩니다.
# 만약 init에서 goal까지 경로가 없다면 함수는 "Fail" 문자열을 반환해야 합니다.
# ----------

# 그리드와 휴리스틱 정보
grid = [[0, 1, 0, 0, 0, 0],
        [0, 1, 0, 0, 0, 0],
        [0, 1, 0, 0, 0, 0],
        [0, 1, 0, 0, 0, 0],
        [0, 0, 0, 0, 1, 0]]

heuristic = [[9, 8, 7, 6, 5, 4],
             [8, 7, 6, 5, 4, 3],
             [7, 6, 5, 4, 3, 2],
             [6, 5, 4, 3, 2, 1],
             [5, 4, 3, 2, 1, 0]]

# 초기 위치와 목표 위치 정의
init = [0, 0]
goal = [len(grid) - 1, len(grid[0]) - 1]
cost = 1  # 이동 비용
delta = [[-1, 0],  # 위로 이동
         [0, -1],  # 왼쪽으로 이동
         [1, 0],   # 아래로 이동
         [0, 1]]   # 오른쪽으로 이동
delta_name = ['^', '<', 'v', '>']  # 이동 방향 이름

def search(grid, init, goal, cost, heuristic):
    # 각 위치가 방문되었는지 기록하는 'closed' 배열 (방문 시 1로 설정)
    closed = [[0 for col in range(len(grid[0]))] for row in range(len(grid))]
    closed[init[0]][init[1]] = 1  # 초기 위치 방문 처리
    
    # 확장 순서를 기록할 'expand' 배열, 초기에는 -1로 설정
    expand = [[-1 for col in range(len(grid[0]))] for row in range(len(grid))]
    
    # 이동 경로 기록용 action 배열, 초기에는 -1로 설정
    action = [[-1 for col in range(len(grid[0]))] for row in range(len(grid))]
    
    # 현재 위치 설정
    x = init[0]
    y = init[1]
    g = 0  # 시작 위치의 경로 비용
    f = g + heuristic[x][y]  # f값은 g와 heuristic의 합
    open = [[f, g, x, y]]  # 탐색할 셀을 [f, g, x, y] 형식으로 open 리스트에 추가
    
    found = False  # 목표 위치를 찾으면 True로 변경되는 플래그
    resign = False  # 경로를 찾을 수 없을 경우 True로 변경되는 플래그
    count = 0  # 확장된 셀의 순서를 기록하는 변수
    
    # 목표를 찾거나 탐색을 종료할 때까지 반복
    while not found and not resign:
        if len(open) == 0:  # open 리스트가 비어 있으면 경로가 없다고 판단
            resign = True
            return "Fail"
        else:
            # open 리스트에서 가장 작은 f 값을 가진 항목을 선택하고 삭제
            open.sort()
            open.reverse()
            next = open.pop()
            
            # 선택한 항목의 g, x, y 값을 가져옴
            g = next[1]
            x = next[2]
            y = next[3]
            f = next[0]
            
            # 현재 위치를 expand 배열에 확장된 순서로 기록
            expand[x][y] = count
            count += 1  # 확장 순서 증가
            
            # 목표 위치에 도달한 경우
            if x == goal[0] and y == goal[1]:
                found = True
            else:
                # 네 방향으로 이동 시도
                for i in range(len(delta)):
                    x2 = x + delta[i][0]
                    y2 = y + delta[i][1]
                    
                    # 이동할 위치가 그리드 안에 있는지 확인
                    if x2 >= 0 and x2 < len(grid) and y2 >= 0 and y2 < len(grid[0]):
                        # 이동 위치가 방문되지 않았고 장애물이 아닌 경우
                        if closed[x2][y2] == 0 and grid[x2][y2] == 0:
                            g2 = g + cost  # 새로운 g값
                            f2 = g2 + heuristic[x2][y2]  # 새로운 f값
                            open.append([f2, g2, x2, y2])  # open 리스트에 추가
                            closed[x2][y2] = 1  # 방문 처리
                            print(next)  # 현재 상태 출력 (디버깅용)
    
    return expand  # 확장된 순서를 포함한 expand 배열 반환

# search 함수 실행 및 결과 출력
print(search(grid, init, goal, cost, heuristic))

""" [0, -1, -1, -1, -1, -1], 
    [1, -1, -1, -1, -1, -1], 
    [2, -1, -1, -1, -1, -1], 
    [3, -1,  8,  9, 10, 11], 
    [4,  5,  6,  7, -1, 12] """
```




dijkstra Algorithm 
==================
A* 알고리즘에서 휴리스틱만 빠진 형태

비용이 최소인 경로를 찾는 데 필요한 정보만을 사용한다. 다익스트라는 모든 경로의 **실제 비용 g(n)g(n)**에만 의존하여 작동하며, 단순히 출발점에서 모든 노드로 가는 최단 경로를 구하는 데 최적화되어 있습니다. 따라서 목표 지점까지의 예상 비용(휴리스틱)을 추가로 고려하지 않아도 됩니다.
A* 알고리즘과 다익스트라 알고리즘의 차이

    다익스트라 알고리즘은 모든 경로의 실제 비용만 사용하기 때문에 균일한 비용 탐색을 수행합니다. 출발점에서 목표 지점까지의 모든 가능한 경로를 탐색하면서 비용이 가장 적게 드는 경로를 선택합니다. 휴리스틱이 없어서 계산 과정이 단순하며, 모든 경로를 평가하는 구조이기 때문에 목표 지점에 도달할 때까지 탐색이 필요해 시간이 많이 소요될 수 있습니다. 하지만 휴리스틱을 고려하지 않기에 최단 경로가 보장됩니다.

    *A 알고리즘**은 다익스트라 알고리즘과 달리 휴리스틱 함수를 추가로 사용하여 목표 지점까지의 예상 비용을 고려해 탐색을 효율적으로 합니다. 이로 인해 목표 지점에 가까운 경로를 우선적으로 탐색하게 되어 불필요한 경로를 덜 탐색하게 됩니다. 정확한 휴리스틱을 사용하면 최단 경로를 더 빨리 찾을 수 있지만, 휴리스틱 계산이 추가되므로 경우에 따라 다익스트라보다 계산이 복잡해질 수 있습니다.

휴리스틱 없는 방식이 더 빠를 때와 효율적인 경우

휴리스틱 없는 방식이 더 빠를 때는 다음과 같은 경우입니다:

    전체 탐색이 필요할 때: 목표 지점뿐 아니라 출발점에서 모든 지점에 대한 최단 경로를 구해야 할 경우, 다익스트라 알고리즘이 더 적합합니다.
    그래프가 작거나 단순한 경우: 목표 지점까지의 예측 비용을 계산하는 것이 오히려 오버헤드가 될 수 있습니다.

```python
import matplotlib.pyplot as plt
import matplotlib.animation as animation
import numpy as np
import heapq

# 그리드 설정 (1: 장애물 추가)
grid = np.array([
    [0, 0, 1, 0, 0, 0],
    [1, 0, 0, 0, 1, 0],
    [0, 0, 1, 0, 1, 0],
    [0, 1, 1, 0, 0, 0],
    [0, 0, 0, 0, 1, 0]
])

# 시작 지점과 목표 지점 설정
start = (0, 0)
goal = (4, 5)

# 이동 방향 설정 (상, 하, 좌, 우)
delta = [(-1, 0), (1, 0), (0, -1), (0, 1)]

def dijkstra(grid, start, goal):
    open_list = []
    heapq.heappush(open_list, (0, start))  # (비용, 위치)
    visited = set()
    visited_nodes = []
    cost_so_far = {start: 0}
    came_from = {}

    while open_list:
        current_cost, current = heapq.heappop(open_list)
        visited_nodes.append(current)

        if current == goal:
            break

        visited.add(current)

        for dx, dy in delta:
            neighbor = (current[0] + dx, current[1] + dy)

            if (0 <= neighbor[0] < grid.shape[0] and
                0 <= neighbor[1] < grid.shape[1] and
                grid[neighbor[0]][neighbor[1]] == 0 and
                neighbor not in visited):
                
                new_cost = current_cost + 1

                if neighbor not in cost_so_far or new_cost < cost_so_far[neighbor]:
                    cost_so_far[neighbor] = new_cost
                    priority = new_cost
                    heapq.heappush(open_list, (priority, neighbor))
                    came_from[neighbor] = current

    # 목표 노드까지의 경로 복원
    path = []
    if current == goal:
        while current in came_from:
            path.append(current)
            current = came_from[current]
        path.append(start)
        path.reverse()

    return path, visited_nodes

# 다익스트라 알고리즘 실행
optimal_path, visited_nodes = dijkstra(grid, start, goal)

# 시각화
fig, ax = plt.subplots()
ax.set_title('dijkstra algo')

def update(num):
    ax.clear()
    ax.imshow(grid, cmap='Greys')

    # 장애물 표시
    obstacles = np.argwhere(grid == 1)
    if obstacles.size > 0:
        x_obs, y_obs = zip(*obstacles)
        ax.plot(y_obs, x_obs, 's', color='black', markersize=10)

    # 방문한 노드 표시
    if num < len(visited_nodes):
        x_vals, y_vals = zip(*visited_nodes[:num+1])
        ax.plot(y_vals, x_vals, 'o', color='yellow', markersize=5)

    # 최적 경로 표시
    if optimal_path and num >= len(visited_nodes) - 1:
        x_opt, y_opt = zip(*optimal_path)
        ax.plot(y_opt, x_opt, color='blue', linewidth=2)

    # 시작 지점과 목표 지점 표시
    ax.plot(start[1], start[0], 's', color='green', markersize=10, label='시작')
    ax.plot(goal[1], goal[0], 's', color='red', markersize=10, label='목표')
    ax.legend()
    ax.set_xticks([])
    ax.set_yticks([])

ani = animation.FuncAnimation(fig, update, frames=len(visited_nodes)+10, interval=200, repeat=False)
plt.show()
```


JPS (Jump Point Search) 알고리즘
==================
**A*알고리즘을 기반으로 한 경로 탐색 알고리즘** 으로, **격자형 그래프(Grid-based Graph)** 에서 최적의 경로를 더 빠르게 찾기 위해 개발되었습니다.
**JPS (Jump Point Search) 알고리즘** 은 **Daniel Harabor**와 **Albin Grastien**이 2011년에 발표한 연구로 탄생했습니다. 
하지만 이 알고리즘이 큰 주목을 받게 된 계기 중 하나가 **스타크래프트 2** 덕분인 것은 맞습니다.
**Blizzard Entertainment**의 **스타크래프트 2 개발진**은 경로 탐색을 최적화하는 데 JPS 알고리즘을 적극적으로 활용했습니다. 
특히, 스타크래프트 2는 대규모 맵에서 수많은 유닛이 동시에 이동하는 RTS(실시간 전략) 게임이기 때문에, 빠르고 효율적인 경로 탐색 알고리즘이 매우 중요했습니다. 
JPS는 이와 같은 환경에 잘 맞아, 격자형 지형에서 경로 탐색 성능을 크게 높이는 데 기여했습니다.

**JPS 알고리즘의 핵심 개념**

**JPS** 는 **점프 포인트 (Jump Points)** 라는 개념을 사용하여 탐색할 노드의 수를 줄입니다. 
여기서 점프 포인트는 최단 경로 탐색에 필요한 중요한 위치로, 반드시 확인해야 하는 노드들입니다. 
이를 통해 중간에 불필요한 노드를 건너뛰며 효율적인 탐색이 가능해집니다.

**JPS 알고리즘의 주요 특징**

  1. **필요한 노드만 탐색** :
      JPS는 단순히 인접한 모든 노드를 탐색하지 않고, 특정 조건을 만족하는 점프 포인트에 해당하는 노드만 탐색합니다.
      예를 들어, 경로가 직선으로 이어질 때는 경로상의 중간 노드를 건너뛰고, 꺾이는 지점이나 장애물을 만나는 지점에서만 추가적인 탐색을 수행합니다.

  2. **강제 이웃 (Forced Neighbors)** :
      강제 이웃은 JPS에서 반드시 탐색해야 하는 인접 노드들입니다. 특정 방향으로 진행 중 장애물을 만나 더 이상 직선 경로로 이동할 수 없는 경우, 대각선 방향으로 이동하거나 경로가 꺾여야 하는 지점들이 강제 이웃에 해당합니다.
      강제 이웃을 점프 포인트로 삼아 효율적인 탐색을 수행합니다.

  3. **점프 및 재귀 탐색** :
      한 방향으로 최대한 점프하며 탐색하고, 도중에 강제 이웃을 만나면 점프를 멈추고 해당 지점을 점프 포인트로 기록합니다.
      각 점프는 재귀적인 방식으로 수행되어, 경로를 진행하며 점프 포인트에 도달할 때까지 다음 점프 포인트를 찾습니다.

**JPS 알고리즘의 작동 과정**

   1. **시작 및 목표 노드 설정** : 시작 지점에서 목표 지점까지 최단 경로를 찾기 위해, 시작 노드에서 출발합니다.

   2. **점프 탐색 시작** : 시작 노드에서 특정 방향으로 점프를 진행합니다.

   3. **점프 포인트 확인**:
       점프 중 강제 이웃을 만나거나 경로가 꺾여야 할 때 점프를 멈춥니다.
       점프 포인트를 확인하여 필요한 경우 해당 지점을 열린 목록에 추가합니다.

   4. **목표 지점 도달 여부 확인** :
       점프 탐색을 반복하여 목표 지점에 도달할 때까지 과정을 진행합니다.

   5. **경로 추적 및 반환**: 목표 지점에 도달한 경우, 최단 경로를 추적하여 반환합니다.

**JPS 알고리즘의 장점과 단점**

   * **장점**:
       탐색 속도 향상: 불필요한 노드를 건너뛰기 때문에, 특히 큰 격자형 그래프에서 탐색 속도가 빠릅니다.
       최단 경로 보장: A* 알고리즘처럼 최단 경로를 보장하면서도 더 적은 노드를 탐색합니다.
   * **단점**:
       격자형 그래프에 특화: 점프 포인트 방식은 격자형 그래프에 최적화되어 있어, 다른 유형의 그래프에서는 효과적이지 않을 수 있습니다.
       복잡성: 점프 포인트와 강제 이웃을 찾는 과정이 복잡할 수 있어, 구현이 다소 까다롭습니다.
        
```python

import matplotlib.pyplot as plt
import matplotlib.animation as animation
import numpy as np
import heapq

# 큰 그리드 생성 (20x20)
np.random.seed(0)
grid_size = (20, 20)
grid = np.zeros(grid_size)
obstacle_probability = 0.2

for i in range(grid_size[0]):
    for j in range(grid_size[1]):
        if np.random.rand() < obstacle_probability:
            grid[i][j] = 1
            
grid[0][0] = 0  # 시작 지점은 빈 공간으로 설정
grid[grid_size[0]-1][grid_size[1]-1] = 0  # 목표 지점은 빈 공간으로 설정

# 시작 지점과 목표 지점 설정
start = (0, 0)
goal = (grid_size[0]-1, grid_size[1]-1)

# 이동 방향 설정 (상, 하, 좌, 우, 대각선 포함)
delta = [(-1, 0), (1, 0), (0, -1), (0, 1),
         (-1, -1), (-1, 1), (1, -1), (1, 1)]

def heuristic(a, b):
    # 맨해튼 거리 사용
    return abs(a[0] - b[0]) + abs(a[1] - b[1])

def is_within_bounds(x, y):
    # 좌표가 그리드의 경계를 벗어나는지 확인하는 함수
    return 0 <= x < grid.shape[0] and 0 <= y < grid.shape[1]

def jump(grid, current, direction):
    x, y = current
    dx, dy = direction
    while True:
        x, y = x + dx, y + dy
        if not is_within_bounds(x, y) or grid[x][y] == 1:
            return None
        if (x, y) == goal:
            return (x, y)
        
        # 직선 경로 상에서 강제 주변 점(Forced Neighbor)을 확인
        if dx != 0 and dy != 0:
            if (is_within_bounds(x - dx, y) and grid[x - dx][y] == 1 and grid[x][y - dy] == 0) or \
               (is_within_bounds(x, y - dy) and grid[x][y - dy] == 1 and grid[x - dx][y] == 0):
                return (x, y)
        elif dx != 0:
            if (is_within_bounds(x, y - 1) and grid[x][y - 1] == 1 and is_within_bounds(x, y + 1) and grid[x][y + 1] == 0) or \
               (is_within_bounds(x, y + 1) and grid[x][y + 1] == 1 and is_within_bounds(x, y - 1) and grid[x][y - 1] == 0):
                return (x, y)
        elif dy != 0:
            if (is_within_bounds(x - 1, y) and grid[x - 1][y] == 1 and is_within_bounds(x + 1, y) and grid[x + 1][y] == 0) or \
               (is_within_bounds(x + 1, y) and grid[x + 1][y] == 1 and is_within_bounds(x - 1, y) and grid[x - 1][y] == 0):
                return (x, y)
        # 점프가 계속 진행되도록 수정
        if dx != 0 or dy != 0:
            return (x, y)  # 강제 이웃을 찾지 못했어도 계속 진행

def jps(grid, start, goal):
    open_list = []
    heapq.heappush(open_list, (heuristic(start, goal), 0, start, [start]))
    visited = set()
    visited.add(start)
    visited_nodes = []
    
    while open_list:
        estimated_total, cost_so_far, current, path = heapq.heappop(open_list)
        visited_nodes.append(current)
        
        if current == goal:
            return path, visited_nodes
        
        for direction in delta:
            jump_point = jump(grid, current, direction)
            if jump_point and jump_point not in visited:
                visited.add(jump_point)
                total_cost = cost_so_far + heuristic(current, jump_point)
                estimated_total = total_cost + heuristic(jump_point, goal)
                heapq.heappush(open_list, (estimated_total, total_cost, jump_point, path + [jump_point]))
                
    return None, visited_nodes

# JPS+ 알고리즘 실행
optimal_path, visited_nodes = jps(grid, start, goal)

# 시각화
fig, ax = plt.subplots(figsize=(6, 6))
ax.set_title('JPS+ 경로 탐색')

def update(num):
    ax.clear()
    ax.imshow(grid, cmap='Greys')
    # 장애물 표시
    obstacles = np.argwhere(grid == 1)
    if obstacles.size > 0:
        x_obs, y_obs = zip(*obstacles)
        ax.plot(y_obs, x_obs, 's', color='black', markersize=4)
    # 방문한 노드 표시
    if visited_nodes:
        x_vals, y_vals = zip(*visited_nodes[:num+1])
        ax.plot(y_vals, x_vals, 'o', color='yellow', markersize=2)
    # 최적 경로 표시
    if optimal_path and num >= len(visited_nodes) - 1:
        x_opt, y_opt = zip(*optimal_path)
        ax.plot(y_opt, x_opt, color='blue', linewidth=2)
    # 시작 지점과 목표 지점 표시
    ax.plot(start[1], start[0], 's', color='green', markersize=8, label='시작')
    ax.plot(goal[1], goal[0], 's', color='red', markersize=8, label='목표')
    ax.legend()
    ax.set_xticks([])
    ax.set_yticks([])

ani = animation.FuncAnimation(fig, update, frames=len(visited_nodes)+20, interval=50, repeat=False)
plt.show()

```



