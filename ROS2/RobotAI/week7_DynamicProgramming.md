## #2 Search

### 학습 목표
~~~
Gragh를 통하여 로봇이 목표지점을 찾아 가는데 있어 최적의(빠른) 경로를 탐색하는 방법을 배움  
~~~

<br>

### 1. Optimal Path (최적 경로 탐색)
---

<img width="1236" alt="스크린샷 2024-11-02 오전 3 51 32" src="https://github.com/user-attachments/assets/3b28fa09-2f1e-4c9d-8bb0-07c4f06347b9">

#### - 실제 예시를 통한 설명   
실제 교통 상황에서 우회전 보다 좌회전이 더 어려움  
우회전과 달리 좌회전 신호를 받아야 하고 그에 따른 시간이 증가 할 수도 있다.  

예제에서는 그에 따라 좌회전시 비용(Cost)을 더 높게(20) 할당하여 진행  

그 결과 시 좌회전 경로는 총 25 Cost를 소모, 돌아가더라도 (직진 + 우회전)은 총 16 Cost로 (직진 + 우회전) 경로가 더 빠르게 도착하게 됨  


<br>

### 2. Dynamic Programming (DP)
---
동적 계획법(DP)은 A* 알고리즘과 마찬가지로 최단 경로를 찾아주는 방법이지만,   
A*와는 다르게 특정 시작 위치에만 국한되지 않고 모든 시작 위치에서 목표 지점까지의 최단 경로를 계산한다.  

#### - 왜 모든 위치를 고려하는지?  
<img width="1266" alt="스크린샷 2024-11-02 오전 4 42 52" src="https://github.com/user-attachments/assets/364b05d8-ede6-439a-ae31-7e72b3b87626">

<br>

위 그림을 통해 예를 들면 자율주행 자동차를 타고 목적지에 도착한다고 가정  

**그림 설명**  
- 왼쪽 파란색 동그라미가 도착하려는 목적지
- 오른쪽 맨 하단 색칠된 파란 네모가 현재 위치  
- 진입 하려는 직진 차선은 2차선

직진 차선 진입 후 1차선으로 변경 하여 좌회전 신호를 받아서 가는 경우   
진입 시 1차선에 이미 큰 트럭이나 여러 대의 자동차들이 있어 차선 변경을 하지 못하거나   
이를 기다리기 위해 멈추었다가 뒤에 오는 차가 경적을 울리면서 상황이 혼잡해질 수 있음  

이처럼 도로에서는 다양한 변수에 의해 상황이 달라질 수 있는 확률적 환경(비결정적 환경)이 존재한다.  
따라서 하나의 경로만이 아니라, 다른 경로에 대한 대체 계획도 필요하게 됨  

<br>

DP는 모든 위치(Postion)에서의 경로 계획을 찾음   

<br>

<img width="1266" alt="스크린샷 2024-11-02 오전 5 12 52" src="https://github.com/user-attachments/assets/89700826-ca9a-425c-abe6-8112b77cef59">

<br>

동적 계획법은 이러한 상황을 대비해 **모든 위치에서의 최적 경로**를 미리 계산하여,   
어느 위치에 있든지 목표 지점으로 가장 효율적으로 이동할 수 있는 계획을 제공한다.   

- 방식  
전체 환경을 격자(grid) 형태로 나누고, 각 격자에 대해 최적의 이동 방향(위, 아래, 왼쪽, 오른쪽)을 사전에 계산함     
이때 각 격자 위치에서 최선의 방향을 나타내는 것을 정책(policy)라고 표현 (Gragh에서 각 노드마다 이동 방향을 시각화 해주기 위함)   
즉, 정책은 각 격자 위치를 특정 방향(위로 이동, 아래로 이동 등)과 매핑해주는 용도   

*위 그림을 보면 각 노드마다 'G'로 갈 수 있는 최단 경로를 보여주고 있음*

#### - 목표 도달 value function (가치 함수)
가치 함수(Value Function)는 특정 상태에서 목표 상태까지 도달하는 데 필요한 최소 비용(이동 횟수 또는 거리)을 정의하는 함수  


<img width="1218" alt="스크린샷 2024-11-02 오전 6 17 02" src="https://github.com/user-attachments/assets/27b72c14-d93a-4e32-a839-8b6a27d8cde6">

<br>

**value function (가치 함수)**

$f(x, y) =
\begin{cases}
0 & \text{if } (x, y) \text{ goal position} \\
\min(f(x{\prime}, y{\prime}) + 1) & \text{if } (x, y) \text{ is not the goal and accessible} \\
99 & \text{if } (x, y) \text{ is a wall or inaccessible}
\end{cases}$

**계산 과정**

목표 위치: $f(2, 3) = 0$ 으로 설정  

$f(2, 2) = f(2, 3) + 1 = 0 + 1 = 1$  
$f(1, 3) = f(2, 3) + 1 = 0 + 1 = 1$

$f(1, 2) = f(1, 3) + 1 = 1 + 1 = 2$  

...  

$f(0, 2) = f(1, 2) + 1 = 2 + 1 = 3$

...

$f(2, 0) = f(1, 0) + 1 = 6 + 1 = 7$

 
이런식으로 **재귀적으로** 목표에 도달하는 Cost를 구 할 수 있다.

**여기서 재귀란?**   
~~~
어떤 문제를 해결하기 위해 알고리즘을 설계할 때 동일한 문제의 조금 더 작은 경우를 해결함으로써 그 문제를 해결하는 것입니다. 
문제가 간단해져서 바로 풀 수 있는 문제로 작아질 때까지 말이죠. 
이런 테크닉을 재귀라고 합니다.

<출처> Khan Academy
~~~

위의 가치 함수는 각 노드에 가장 인접한 값을 기반으로 Cost를 구하게 되고  
$f(x, y)$는 인접한 $f(x', y')$의 가치에 의존  
(즉, 현재 노드의 값은 이전 인접한 노드에 의존적)

즉, 현재 셀의 Cost는 이전 인접한 셀들의 값에 기반하여 업데이트됨  


<br>

**코드 구현**  

~~~python
grid = [[0, 0, 1, 0, 0, 0],
        [0, 0, 1, 0, 0, 0],
        [0, 0, 1, 0, 1, 0],
        [0, 0, 1, 0, 1, 0],
        [0, 0, 0, 0, 1, 0]]
value = [[99 for col in range(len(grid[0]))] for row in range(len(grid))]

delta = [[-1, 0 ], # go up
        [ 0, -1], # go left
        [ 1, 0 ], # go down
        [ 0, 1 ]] # go right

delta_name = ['^', '<', 'v', '>']

...
def compute_value(grid,goal,cost):

    ...

    elif grid[x][y] == 0:
        for a in range(len(delta)):
            x2 = x + delta[a][0]
            y2 = y + delta[a][1]
            if x2 >= 0 and x2 < len(grid) and y2 >= 0 and y2 < len(grid[0]) and grid[x2][y2] == 0:
                # 이 부분이 f(x, y) = min f(x', y') + 1
                v2 = value[x2][y2] + cost_step
                # v2가 현재 노드의 값보다 작은지를 확인 만약 작다면, 그리드에서 해당 노드의 값을 업데이트
                if v2 < value[x][y]:
                    change = True
                    value[x][y] = v2
~~~

**실행 결과**
~~~
# Result

[11, 99, 7, 6, 5, 4]
[10, 99, 6, 5, 4, 3]
[9, 99, 5, 4, 3, 2]
[8, 99, 4, 3, 2, 1]
[7, 6, 5, 4, 99, 0]

# Optimal Policy

['v', ' ', 'v', 'v', 'v', 'v']
['v', ' ', 'v', 'v', 'v', 'v']
['v', ' ', 'v', 'v', 'v', 'v']
['v', ' ', '>', '>', '>', 'v']
['>', '>', '^', '^', ' ', '*']
~~~

#### - Left Turn Policy (좌회전 정책)

위 좌회전 , (직진 + 우회전) 예시를 참고하여 좌회전시 Cost를 더욱 많이 주어  
최적의 경로를 탐색하는 과정을 구현

<br>

~~~python
# 방향별 이동 (상, 좌, 하, 우)
# 각 방향은 이동 후의 [행 변화, 열 변화]를 정의합니다.
forward = [[-1, 0],  # 위쪽으로 이동
           [0, -1],  # 왼쪽으로 이동
           [1, 0],  # 아래쪽으로 이동
           [0, 1]]  # 오른쪽으로 이동

# 이동 방향의 이름 (상, 좌, 하, 우)
forward_name = ['up', 'left', 'down', 'right']

# 가능한 액션: 우회전(-1), 직진(0), 좌회전(1)
# -1은 현재 방향 기준 우회전, 0은 직진, 1은 좌회전
action = [-1, 0, 1]

# 액션을 수행할 때의 이름 (우회전, 직진, 좌회전)
action_name = ['R', '#', 'L']

# 그리드 초기화
# 0은 이동 가능, 1은 장애물로 이동 불가능
grid = [[1, 1, 1, 0, 0, 0],
        [1, 1, 1, 0, 1, 0],
        [0, 0, 0, 0, 0, 0],
        [1, 1, 1, 0, 1, 1],
        [1, 1, 1, 0, 1, 1]]

# 초기 위치 및 방향 설정
# [행, 열, 방향]으로 구성, 방향은 위쪽을 0으로 설정
init = [4, 3, 0]  # 시작 위치는 [4, 3]이고 방향은 '상'

# 목표 지점 위치 설정 [행, 열]
goal = [2, 0]

# 각 액션에 대한 비용: [우회전 비용, 직진 비용, 좌회전 비용]
cost = [2, 1, 20]


def optimum_policy2D(grid, init, goal, cost):
    # 3차원 리스트 초기화
    # 가치 함수(value): 각 셀에서 목표까지 최소 비용을 저장
    # 초기에는 모든 값이 큰 값(999)으로 설정되어 있음
    value = [[[999 for col in range(len(grid[0]))] for row in range(len(grid))] for orientation in range(4)]

    # 정책(policy): 각 셀과 방향에서의 최적 액션을 저장
    policy = [[[' ' for col in range(len(grid[0]))] for row in range(len(grid))] for orientation in range(4)]

    # 최종 정책을 담을 2D 리스트, 최적 경로가 표시됨
    policy2D = [[' ' for col in range(len(grid[0]))] for row in range(len(grid))]

    change = True  # 변화가 있는지 확인하는 플래그
    while change:
        change = False
        # 그리드의 모든 위치(x, y)와 방향(orientation)을 탐색
        for x in range(len(grid)):
            for y in range(len(grid[0])):
                for orientation in range(4):
                    # 목표 지점에서 가치 함수 초기화 (비용 0)
                    if x == goal[0] and y == goal[1]:
                        if value[orientation][x][y] != 0:
                            value[orientation][x][y] = 0
                            policy[orientation][x][y] = '*'  # 목표 지점은 '*'로 표시
                            change = True

                    # 현재 위치가 이동 가능한 경우
                    elif grid[x][y] == 0:
                        # 가능한 모든 액션(우회전, 직진, 좌회전)에 대해 반복
                        for i in range(len(action)):
                            # 새로운 방향을 계산: (현재 방향 + 액션 값) % 4
                            o2 = (orientation + action[i]) % 4
                            # 새로운 위치 계산
                            x2 = x + forward[o2][0]
                            y2 = y + forward[o2][1]

                            # 새로운 위치가 그리드 내에 있고 이동 가능할 경우
                            if 0 <= x2 < len(grid) and 0 <= y2 < len(grid[0]) and grid[x2][y2] == 0:
                                # 새로운 위치에서의 가치 값 계산
                                v2 = value[o2][x2][y2] + cost[i]
                                # 현재 가치보다 작으면 업데이트
                                if v2 < value[orientation][x][y]:
                                    value[orientation][x][y] = v2
                                    policy[orientation][x][y] = action_name[i]  # 최적 액션 저장
                                    change = True  # 변화가 있었으므로 반복을 계속함


    # 최적의 정책 추출
    # 초기 위치 및 방향에서 출발하여 policy2D에 최적 경로 표시
    x, y, orientation = init
    policy2D[x][y] = policy[orientation][x][y]

    # 목표 지점에 도달할 때까지 경로 따라가기
    while policy[orientation][x][y] != '*':
        # 현재 위치에서 다음 액션에 따라 방향을 업데이트
        if policy[orientation][x][y] == 'R':
            o2 = (orientation - 1) % 4  # 우회전
        elif policy[orientation][x][y] == '#':
            o2 = orientation  # 직진
        elif policy[orientation][x][y] == 'L':
            o2 = (orientation + 1) % 4  # 좌회전

        # 다음 위치로 이동
        x += forward[o2][0]
        y += forward[o2][1]
        orientation = o2

        policy2D[x][y] = policy[orientation][x][y]  # 경로에 액션 표시

    return policy2D  # 최적의 정책을 담은 2D 리스트 반환


# 함수 실행 및 결과 출력
policy2D = optimum_policy2D(grid, init, goal, cost)
print("최적의 정책:")
for row in policy2D:
    print(row)
~~~

<br>

모든 위치마다 4 방향을 모두 계산함

~~~python
forward = [[-1, 0],  # 위쪽으로 이동 (0번 방향)
          [0, -1],   # 왼쪽으로 이동 (1번 방향) 
          [1, 0],    # 아래쪽으로 이동 (2번 방향)
          [0, 1]]    # 오른쪽으로 이동 (3번 방향)
~~~

~~~
위쪽(0)을 향할 때: ↑
왼쪽(1)을 향할 때: ←
아래쪽(2)을 향할 때: ↓
오른쪽(3)을 향할 때: →
~~~

<br>

#### - policy2D 업데이트 부분 
~~~python
 while policy[orientation][x][y] != '*':
        # 현재 위치에서 다음 액션에 따라 방향을 업데이트
        if policy[orientation][x][y] == 'R':
            o2 = (orientation - 1) % 4  # 우회전
        elif policy[orientation][x][y] == '#':
            o2 = orientation  # 직진
        elif policy[orientation][x][y] == 'L':
            o2 = (orientation + 1) % 4  # 좌회전

        # 다음 위치로 이동
        x += forward[o2][0]
        y += forward[o2][1]
        orientation = o2

        policy2D[x][y] = policy[orientation][x][y]  # 경로에 액션 표시
~~~

~~~
[' ', ' ', ' ', ' ', ' ', ' ']
[' ', ' ', ' ', ' ', ' ', ' ']
[' ', ' ', ' ', ' ', ' ', ' ']
[' ', ' ', ' ', '#', ' ', ' ']
[' ', ' ', ' ', '#', ' ', ' ']


[' ', ' ', ' ', ' ', ' ', ' ']
[' ', ' ', ' ', ' ', ' ', ' ']
[' ', ' ', ' ', '#', ' ', ' ']
[' ', ' ', ' ', '#', ' ', ' ']
[' ', ' ', ' ', '#', ' ', ' ']


[' ', ' ', ' ', ' ', ' ', ' ']
[' ', ' ', ' ', '#', ' ', ' ']
[' ', ' ', ' ', '#', ' ', ' ']
[' ', ' ', ' ', '#', ' ', ' ']
[' ', ' ', ' ', '#', ' ', ' ']


[' ', ' ', ' ', 'R', ' ', ' ']
[' ', ' ', ' ', '#', ' ', ' ']
[' ', ' ', ' ', '#', ' ', ' ']
[' ', ' ', ' ', '#', ' ', ' ']
[' ', ' ', ' ', '#', ' ', ' ']


[' ', ' ', ' ', 'R', '#', ' ']
[' ', ' ', ' ', '#', ' ', ' ']
[' ', ' ', ' ', '#', ' ', ' ']
[' ', ' ', ' ', '#', ' ', ' ']
[' ', ' ', ' ', '#', ' ', ' ']


[' ', ' ', ' ', 'R', '#', 'R']
[' ', ' ', ' ', '#', ' ', ' ']
[' ', ' ', ' ', '#', ' ', ' ']
[' ', ' ', ' ', '#', ' ', ' ']
[' ', ' ', ' ', '#', ' ', ' ']


[' ', ' ', ' ', 'R', '#', 'R']
[' ', ' ', ' ', '#', ' ', '#']
[' ', ' ', ' ', '#', ' ', ' ']
[' ', ' ', ' ', '#', ' ', ' ']
[' ', ' ', ' ', '#', ' ', ' ']


[' ', ' ', ' ', 'R', '#', 'R']
[' ', ' ', ' ', '#', ' ', '#']
[' ', ' ', ' ', '#', ' ', 'R']
[' ', ' ', ' ', '#', ' ', ' ']
[' ', ' ', ' ', '#', ' ', ' ']


[' ', ' ', ' ', 'R', '#', 'R']
[' ', ' ', ' ', '#', ' ', '#']
[' ', ' ', ' ', '#', '#', 'R']
[' ', ' ', ' ', '#', ' ', ' ']
[' ', ' ', ' ', '#', ' ', ' ']


[' ', ' ', ' ', 'R', '#', 'R']
[' ', ' ', ' ', '#', ' ', '#']
[' ', ' ', ' ', '#', '#', 'R']
[' ', ' ', ' ', '#', ' ', ' ']
[' ', ' ', ' ', '#', ' ', ' ']


[' ', ' ', ' ', 'R', '#', 'R']
[' ', ' ', ' ', '#', ' ', '#']
[' ', ' ', '#', '#', '#', 'R']
[' ', ' ', ' ', '#', ' ', ' ']
[' ', ' ', ' ', '#', ' ', ' ']


[' ', ' ', ' ', 'R', '#', 'R']
[' ', ' ', ' ', '#', ' ', '#']
[' ', '#', '#', '#', '#', 'R']
[' ', ' ', ' ', '#', ' ', ' ']
[' ', ' ', ' ', '#', ' ', ' ']


[' ', ' ', ' ', 'R', '#', 'R']
[' ', ' ', ' ', '#', ' ', '#']
['*', '#', '#', '#', '#', 'R']
[' ', ' ', ' ', '#', ' ', ' ']
[' ', ' ', ' ', '#', ' ', ' ']
~~~





### 4. Stochastic(확률적) Value Iteration 알고리즘  
---

~~~python
# 확률적 가치 반복(Stochastic Value Iteration) 함수 구현
# 네 가지 이동 방향과 그에 대응하는 방향 기호 정의
delta = [[-1, 0],  # 위로 이동
         [0, -1],  # 왼쪽으로 이동
         [1, 0],   # 아래로 이동
         [0, 1]]   # 오른쪽으로 이동
delta_name = ['^', '<', 'v', '>']

def stochastic_value(grid, goal, cost_step, collision_cost, success_prob):
    # 실패 확률을 계산하여 좌우로 이동할 확률(failure_prob)을 구함
    failure_prob = (1.0 - success_prob) / 2.0
    
    # value: 모든 셀의 초기 가치를 충돌 비용(collision_cost)로 설정
    value = [[collision_cost for _ in range(len(grid[0]))] for _ in range(len(grid))]
    
    # policy: 최적의 정책을 표시하기 위해 각 셀을 공백으로 초기화
    policy = [[' ' for _ in range(len(grid[0]))] for _ in range(len(grid))]

    change = True  # 변화 여부를 확인하기 위한 변수

    # 가치 반복 알고리즘 실행
    while change:
        change = False  # 반복을 시작할 때 변화를 False로 초기화

        # 그리드의 모든 셀을 순회하면서 가치와 정책을 업데이트
        for x in range(len(grid)):
            for y in range(len(grid[0])):
                
                # 목표 지점의 경우 가치를 0으로 설정
                if [x, y] == goal:
                    if value[x][y] > 0:
                        value[x][y] = 0
                        change = True  # 목표 지점의 값이 바뀌므로 변화 발생

                # 이동 가능한 셀의 경우 가치 계산
                elif grid[x][y] == 0:
                    # 네 방향의 이동(상, 좌, 하, 우)에 대해 계산
                    """
                    # 직진 시도 (50% 확률)
                    - 위로 이동 성공하면: 0.5 * (다음 위치의 value)
                    - 벽에 충돌하면: 0.5 * collision_cost(100)

                    # 왼쪽으로 빗나감 (25% 확률)
                    - 왼쪽으로 이동 성공하면: 0.25 * (왼쪽 위치의 value)
                    - 벽에 충돌하면: 0.25 * collision_cost(100)

                    # 오른쪽으로 빗나감 (25% 확률)
                    - 오른쪽으로 이동 성공하면: 0.25 * (오른쪽 위치의 value)
                    - 벽에 충돌하면: 0.25 * collision_cost(100)

                    # 총 기대 비용 = cost_step + (각 경우의 확률 * 결과 비용의 합)
                    """
                    for a in range(len(delta)):
                        v2 = cost_step  # 기본 이동 비용

                        # 좌회전, 직진, 우회전에 따른 결과를 탐색
                        for i in range(-1, 2):
                            a2 = (a + i) % len(delta)  # 실제 이동할 방향의 인덱스
                            x2 = x + delta[a2][0]      # 이동 후 x 좌표
                            y2 = y + delta[a2][1]      # 이동 후 y 좌표

                            """
                            v2는 현재 액션을 선택했을 때의 기대 비용입니다.
                            i는 -1, 0, 1로, 좌회전, 직진, 우회전을 의미합니다.
                            a2는 실제로 이동하게 될 방향의 인덱스입니다.
                            이동 후의 좌표 (x2, y2)를 계산합니다.
                            """

                            # 확률에 따른 비용 합산
                            prob = success_prob if i == 0 else failure_prob
                            """
                            i == 0인 경우는 의도한 방향으로의 이동이므로 success_prob를 사용합니다, 
                            그 외의 경우는 좌우로의 이동이므로 failure_prob를 사용합니다.
                            """

                            # 그리드 안에 있고 이동 가능한 경우 기대 비용을 누적
                            """
                            이동한 위치가 그리드 내에 있고, 이동 가능한 경우:해당 셀의 가치를 가져와서 확률을 곱하여 v2에 더합니다.
                            이동할 수 없는 경우(그리드를 벗어나거나 장애물인 경우):충돌 비용(collision_cost)에 확률을 곱하여 v2에 더합니다.
                            """
                            if 0 <= x2 < len(grid) and 0 <= y2 < len(grid[0]) and grid[x2][y2] == 0:
                                v2 += prob * value[x2][y2]
                            else:
                                # 이동 불가능한 경우 충돌 비용을 기대 비용에 더함
                                v2 += prob * collision_cost

                        # 계산된 기대 비용이 현재 가치보다 낮으면 가치와 정책을 업데이트
                        """
                        계산된 기대 비용 v2가 현재 셀의 가치보다 작으면:
                        value와 policy를 업데이트합니다.
                        change를 True로 설정하여 다음 반복을 진행합니다
                        """
                        if v2 < value[x][y]:
                            print("기존 값:", value[x][y], "갱신 값:", v2)  # 업데이트 전후의 가치 출력
                            value[x][y] = v2  # 가치 업데이트
                            policy[x][y] = delta_name[a]  # 최적의 방향 업데이트
                            change = True  # 변화 발생
                        

    return value, policy  # 가치와 정책을 반환

# 테스트를 위한 그리드 환경 정의
grid = [[0, 0, 0, 0],
        [0, 0, 0, 0],
        [0, 0, 0, 0],
        [0, 1, 1, 0]]
goal = [0, len(grid[0]) - 1]  # 목표 위치는 맨 위 오른쪽 모서리
cost_step = 1
collision_cost = 100
success_prob = 0.5

# 함수 실행 및 결과 출력
value, policy = stochastic_value(grid, goal, cost_step, collision_cost, success_prob)
for row in value:
    print(row)
for row in policy:
    print(row)
~~~

~~~python
delta = [[-1, 0],  # 0: 위
         [0, -1],  # 1: 왼쪽
         [1, 0],   # 2: 아래
         [0, 1]]   # 3: 오른쪽
delta_name = ['^', '<', 'v', '>']  # 각 방향을 나타내는 기호

# 움직임의 확률
success_prob = 0.5      # 의도한 방향으로 이동할 확률
failure_prob = 0.25     # 좌/우로 빗나갈 확률 (각각)

~~~

~~~
# 예: 위쪽(^)으로 가려고 할 때
# - 50% 확률로 위로 이동
# - 25% 확률로 왼쪽으로 이동
# - 25% 확률로 오른쪽으로 이동
~~~

<br>


~~~
가치 함수 업데이트 예시:
현재 위치 (x, y)에서 위로 이동하려고 할 때:
성공적으로 위로 이동한 경우:
    확률: success_prob (예: 0.5)
    비용: cost_step + value[x-1][y]
왼쪽으로 이동한 경우:
    확률: failure_prob (예: 0.25)
    비용: cost_step + value[x][y-1]
오른쪽으로 이동한 경우:
    확률: failure_prob (예: 0.25)
    비용: cost_step + value[x][y+1]
총 기대 비용 v2는 각 경우의 비용에 해당 확률을 곱한 값을 합산한 것입니다.

코드의 주요 변수 및 함수 설명:
delta와 delta_name:
    가능한 이동 방향과 그에 대응하는 이름을 정의합니다.
    delta는 이동 벡터를 나타내고, delta_name은 시각화를 위한 기호입니다.
grid:
    환경의 맵을 나타내는 2차원 리스트로, 이동 가능 여부를 표시합니다.
value:
    각 셀의 가치(value)를 저장하는 2차원 리스트입니다.
    초기에는 모든 셀이 collision_cost로 설정되어 있습니다.
policy:
    각 셀에서의 최적 정책(방향)을 저장하는 2차원 리스트입니다.
    
cost_step: 이동 시 발생하는 기본 비용입니다.
collision_cost: 충돌 시 발생하는 비용으로, 매우 큰 값으로 설정하여 충돌을 피하도록 합니다.
success_prob: 의도한 방향으로 이동할 확률입니다.
failure_prob: 좌우 방향으로 이동할 확률로, (1 - success_prob) / 2로 계산됩니다.

~~~