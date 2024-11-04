# 8차시: PID Control

> 자율주행의 안정성 보상
> 

### 인트로

1. PID 제어
    
    : 로봇의 위치와 방향을 정확히 조절하기 위해 필요한 기법
    
    - 비례(P) 제어
        
        : **오차에 비례**해 제어 값 조정, 즉각적인 반응 제공
        
        - 현재 오차에 반응
    - 적분(I) 제어
        
        : **오차의 누적을 계산**해 보정하여 목표에 도달하게 함
        
        - 누적된 오차 해결
    - 미분(D) 제어
        
        : **오차의 변화 속도**에 따라 제어 값을 조정해 안정성 높임 
        
        - 오차 변화 속도에 따라 보정
    - 경로 스무딩 알고리즘 (Smoothing Algorithm)
        
        : 로봇이 경로를 따라 이동할 때 급격한 회전을 줄이고 자연스러운 이동을 위해 경로를 부드럽게 하는 기법 
        
        - 가중치를 설정하여 각 점의 이동 경로를 평활화
            
            ⇒ 각 파라미터의 변화가 어떻게 로봇의 동작에 영향을 미치는지 실험 
            
        - 로봇이 목표지점까지 부드럽게 이동하도록 도움
    - 로봇 클래스의 구현
        - 로봇의 초기 위치 및 각도 설정하고 이동을 구현
        - PID 제어 변수를 활용해 로봇의 방향을 조정하는 방식
    
    > 제어 방식의 차이 시각적 확인 필요 ⇒ 로봇의 경로가 부드럽게 변화하는 과정
    > 
    - P-Controller
        - 단순 비례 제어를 통해 로봇의 방향을 조정하는 방법
        - 오버슈트 발생의 위험
    - PD 및 PID 제어기 구현
        - PD 제어기: 비례와 미분 제어 결합
            
            ⇒ 오버슈트 줄이면서 안정적 제어 제공
            
        - PID 제어기: 방향 오차 최소화, 로봇의 경로 이탈 보정 기능
2. PID 제어와 Search 방법의 차이
    1. Search 방법: “경로 찾기”
        - 로봇이 목적지까지 갈 수 있는 최적의 경로를 찾기 위해 사용하는 기법
        - Search 알고리즘: A* 알고리즘, Dijkstra 알고리즘 ⇒ 장애물 피하거나 최단 경로 계산
        - 경로가 계산되면 로봇이 어떤 점을 따라가야 하는지 알려주는 데 집중
    2. PID 제어: “그 경로를 따라 이동하도록 제어” 
        - 이미 **주어진 경로**를 따라서 이동하는 제어 기법
            
            ⇒ 찾은 경로를 실제 모션 명령으로 변환! 
            
            & 부드러운 경로 생성 목적 
            
            ![image](https://github.com/user-attachments/assets/f75e5e8b-26a1-4791-bf08-df84def70191)
            
        - 경로가 주어졌을 때, 로봇이 그 **경로에서 얼마나 벗어났는지(오차)**를 측정 ⇒ 이를 수정하여 **매끄럽게 경로를 따라가도록** 도움
        - 경로를 따르며 조정하는 데 집중
        - 로봇이 흔들리지 않고 자연스럽게 이동하도록 조향이나 속도 조정

### Smoothing Algorithm (weight_data)

1. 목적
    - 로봇이 이동할 때, 단순한 직선 경로나 급격한 방향 전환은 로봇의 이동을 어색하게 만듦
    - 이 알고리즘은 경로를 부드럽게 바꿔주어, 로봇이 더 자연스럽게 움직이도록 함
2. 원리
    - **경로의 각 지점을 약간씩 이동시키면서 전체 경로가 자연스럽게 이어지도록** 함
    - 가중치
        - weight_data: **원래 경로 점에 가까이 있도록** 하는 가중치
        - weight_smoooth: 경로가 **매끄럽도록** 하는 가중치
    - 경로의 첫 점과 마지막 점은 고정됨, 그 사이 점들만 부드럽게 조정함
3. 반복적 업데이트 과정
    - 이 알고리즘은 경로 점들을 **반복적으로 조정**
    - 각 점에 대해 **weight_data와 weight_smooth**를 기반으로 조정 → 각 점이 원래 경로와 크게 벗어나지 않으면서도 부드럽게 이어지도록 함
    - 파이썬 코드를 보면 경로의 각 점을 반복적으로 조정하는 과정 있음
    - smooth() 함수는 경로 점을 복사한 뒤, 지정된 가중치에 따라 점들을 반복적으로 조정하여 최종적으로 매끄러운 경로 반환
4. 파이썬 코드
    - 스무딩 함수
        - 경로의 시작과 끝은 고정
        - 중간 부분만 스무딩을 통해 부드럽게 만드는 함수
        - 경사 하강 방법을 사용하여 경로 부드럽게 조정
    
    ```python
    # 기존의 경로를 복사하여 newpath 저장하는 데 사용
    # -> 원래 경로는 변경되지 않고 newpath만 수정됨 
    from copy import deepcopy
    
    # 원래 경로와 스무딩된 경로 나란히 출력 
    # 각 좌표 점을 비교하여 어떤 변화가 생겼는지 보여줌 
    def printpaths(path,newpath):
        for old,new in zip(path,newpath):
            print( '['+ ', '.join('%.3f'%x for x in old) + '] -> ['+ ', '.join('%.3f'%x for x in new) +']')
    
    # 기존의 경로 좌표를 저장한 리스트 
    # 각 리스트 항목: [x,y] 형태의 좌표
    path = [[0, 0],
            [0, 1],
            [0, 2],
            [1, 2],
            [2, 2],
            [3, 2],
            [4, 2],
            [4, 3],
            [4, 4]]
            
    # smooth() 함수: 경로를 부드럽게 만드는 함수
    # 파라미터: weight_data, weight_smooth, tolerance
    # weight_data: 원래 경로와의 거리 유지 조절
    # weight_smooth: 각 점이 인접한 점들과 부드럽게 이어지도록 조정
    # tolerance: 원하는 정확도 수준, 반복을 멈추는 기준 
    def smooth(path, weight_data = 0.5, weight_smooth = 0.1, tolerance = 0.000001):
        # Make a deep copy of path into newpath
        # path의 복사본-> 스무딩 과정에서 변경됨 
        # deepcopy를 사용하여 원래의 path값은 변경되지 않도록 보호 
        newpath = deepcopy(path)
    		
    		# 스무딩 과정에서 변경된 값의 합계를 추적
    		# 초기 값 = tolerance 
        change = tolerance
        
        while change >= tolerance:
    		    # 각 반복에서 change를 0으로 초기화 
            change = 0
            # 경로의 첫 번째와 마지막 점을 제외하고 중간 점들만 스무딩 
            for i in range(1, len(path) - 1):
    		        # 각 좌표 값(x와 y)에 대해 스무딩 적용 
                for j in range(len(path[0])):  
    		            # 원래 경로와 스무딩된 경로의 차이를 weight_data만큼 반영한 값
    		            # 원래 경로에 가까이 있도록 함 
                    d1 = weight_data*(path[i][ j] - newpath[i][ j])
                    # 인접한 두 점을 기준으로 부드럽게 이어지도록 설정한 값
                    # weight_smooth로 가중치 적용 
                    d2 = weight_smooth*(newpath[i-1][ j] + newpath[i+1][ j] - 2*newpath[i][ j])
                    # 각 점의 변경량 누적 
                    change += abs(d1 + d2)
                    # newpath 좌표 값 업뎃 
                    newpath[i][ j] += d1 + d2
        # 부드러워진 최종 경로 반환 
        return newpath
    
    # 원래 경로(path)와 부드럽게 스무딩된 경로(smooth(path))를 나란히 출력해 비교하는 코드
    printpaths(path,smooth(path))
    ```
    
    ```python
    [0.000, 0.000] -> [0.000, 0.000]
    [0.000, 1.000] -> [0.021, 0.979]
    [0.000, 2.000] -> [0.149, 1.851]
    [1.000, 2.000] -> [1.021, 1.979]
    [2.000, 2.000] -> [2.000, 2.000]
    [3.000, 2.000] -> [2.979, 2.021]
    [4.000, 2.000] -> [3.851, 2.149]
    [4.000, 3.000] -> [3.979, 3.021]
    [4.000, 4.000] -> [4.000, 4.000]
    ```
    
    ```python
    # 다양한 weight_data 값을 설정
    weight_data_values = [0.1, 0.3, 0.5, 0.7, 0.9]
    
    # 각 weight_data에 대한 스무딩 경로 계산
    smoothed_paths = [smooth(path, weight_data=w, weight_smooth=0.1) for w in weight_data_values]
    ```
    
    ![%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7_2024-10-31_18-29-15](https://github.com/user-attachments/assets/7595dae0-6ff9-42c6-ab75-1724f88c1f9b)
    
    ⇒ 평활화(평탄화): 피크가 팍팍 튀는 노이즈를 부드럽게 만들어주는 기술 
    
- zero data weight (weight_smooth)
    - 스무딩 알고리즘에서 weight_data = 0 으로 설정된 상태
    - weight_data는 원래 경로와의 거리를 유지하려는 가중치인데, 이를 0으로 두면 원래 경로 점의 영향을 전혀 받지 않게 됨
    - 모든 중간 점이 인접한 점들과 최대한 부드럽게 이어지도록 조정됨

### PID 시스템 이해(영상)

- plant: 우리가 제어하고자 하는 시스템 또는 동작에 영향을 주고자 하는 시스템
- plat 입력: 동작 신호, plant 출력: 제어 변수(설정값, 참조, 목표값)
    
    ⇒ 시스템에서 우리가 원하는 제어 변수, 즉 출력을 산출할 수 있도록 적절한 동작 신호, 즉 입력을 생성하는 방법을 파악하는 것 
    
- 피드백 제어에서 시스템의 출력은 피드백을 전달함 ⇒ 명령과 비교하여 우리가 원하는 지점과 시스템 사이의 차이(오차)를 확인
    - 오차 0인 경우: 출력이 명령한 그대로 나오는 값 (=목표값)
- 문제: 어떻게 이 오차를 적절한 액추에이터 명령으로 변환하여, 시간이 지남에 따라 오차를 0으로 수렴하게 만들까? ⇒ “제어기”
    - 예를들어 오차가 50일 때, 제어기 값을 0.1로 하면 초당 5 정도로 속도가 늦춰져 오차를 빠르게 줄일 수 있음
    - 목표 라인에서 정확히 멈추게 해줄 것

### PID

- 바퀴가 어느 방향을 바라보고 있느냐? ⇒ 이동방향이 달라짐
- 평활화 기술로 차량 운행
- 어떤 방식으로 차량의 조향 각도 설정?
    1. 운전대 일정한 유지
    2. 랜덤 스티어링 명령
    3. **크로스트랙 오차 (=CTE)에 의한 조향 각도 설정** 
        
        ![image 1](https://github.com/user-attachments/assets/1b177091-8de2-4ed1-badd-ce57cab8ed8f)
        
        - CTE: 차가 수평선상에 있을 때, 가로축과 경계선 사이 보폭 ; 얼마만큼 떨어졌나 에러율
            - 로봇의 현재 위치와 목표 경로 간의 수직 거리로, 이 값을 사용하여 로봇이 얼마나 경로에서 벗어나 있는지 측정
            - 로봇이 경로의 왼쪽에 있으면 CTE는 양수로, 오른쪽에 있으면 음수로 표현
            - 로봇이 경로에 정확히 위치해 있을 경우 CTE는 0 (=로봇이 목표 경로를 완벽하게 추적하고 있음을 의미)
            - CTE가 크면 로봇은 그 방향으로 조향을 더 크게 해야 하고, CTE가 작으면 조향을 적게 해야 (CTE에 따라 조향을 조정)
        - 적절한 CTE 적용을 통해 스티어링(=조향 장치) 일관성 있게 유지
        - CTE 에러 기준으로 방향을 잡아야 함
        - CTE 오차(간격)이 클수록 더 많은 방향을 바꿀 수 있음

---

### P-Controller ; “CTE”

- 비례 제어기
- 비례 정도
    - **조향각도(알파)는 CTE*타우(일부요인)**과 비례
- 조향각도를 적절히 잡아주지 않으면, 자율주행이 일관되게 지속적으로 앞으로 나가는게 쉽지 않음
    
    ![image 2](https://github.com/user-attachments/assets/695f899d-dcbe-40cc-a637-391e5953e002)
    
    ⇒ 전진을 목적으로 하고 있지만, 타우(일부요인)으로 인해 초록색 방향으로 오버슈팅 발생 
    
- P-Controller은 약간의 오버슈트 & 괜찮은 normal 정도 → 약간 안정적 (절대 수렴은 x)
    
    ![image 3](https://github.com/user-attachments/assets/4837bf1e-90f6-4028-bdd7-89cf24667338)
    
- CTE를 잡는게 스티어링의 핵심 ⇒ CTE에서 나오는 신호 안정되게 ⇒ 그래야 앞으로 쭉 직진 가능
    
    ![aeed3fbc-d0c6-4b72-9227-64499e32e389](https://github.com/user-attachments/assets/01ca1e9c-74fc-49bc-88e8-e082398baceb)
    
- P-Controller = 로봇의 이동 경로 제어
- 로봇이 y축에서 벗어난 정도를 측정해 조향 각도를 조정하면서 x축을 따라 이동하는 방식

```python
# 100번의 반복을 실행하여 P 컨트롤러를 구현합니다.
# 로봇 모션. 원하는 궤적
# 로봇은 x축입니다. 조향 각도를 설정해야 합니다.
# 매개변수 tau를 사용하여 다음을 수행합니다.
#
# steering = -tau * crosstrack_error

from math import *
import random

# ------------------------------------------------
#
# this is the robot class
#

# 로봇의 위치, 방향, 이동 방법 정의 
class robot:
    # --------
    # init:
    # creates robot and initializes location/orientation to 0, 0, 0
    #
		
		 
    def __init__(self, length = 20.0):
		    # 로봇의 초기 위치(x, y)
        self.x = 0.0
        self.y = 0.0
        # 방향
        self.orientation = 0.0
        # 로봇의 길이 
        self.length = length
				
				# 이동 시 적용할 노이즈, 드리프트 
        self.steering_noise = 0.0
        self.distance_noise = 0.0
        self.steering_drift = 0.0

    # --------
    # set:
    #    sets a robot coordinate
    #
		
		# 로봇의 위치와 방향 설정 
    def set(self, new_x, new_y, new_orientation):
        self.x = float(new_x)
        self.y = float(new_y)
        # 새로운 방향은 2파이로 나눠 범위가 0~2파이 사이가 되도록 함 
        self.orientation = float(new_orientation) % (2.0 * pi)

    # --------
    # set_noise:
    #    sets the noise parameters
    #
    
    # 조향과 거리 이동에 노이즈를 추가해 로봇의 움직임이 더 불확실해지도록 설정 
    def set_noise(self, new_s_noise, new_d_noise):
        # makes it possible to change the noise parameters
        # this is often useful in particle filters
        self.steering_noise = float(new_s_noise)
        self.distance_noise = float(new_d_noise)

    # --------
    # set_steering_drift:
    #    sets the systematical steering drift parameter
    #
		
		# 조향 드리프트를 설정해 로봇이 원래 경로에서 조금씩 벗어나는 상황을 시뮬레이션
    def set_steering_drift(self, drift):
        self.steering_drift = drift

    # --------
    # move:
    # steering = front wheel steering angle, limited by max_steering_angle
    # distance = total distance driven, must be non-negative
		
		# 로봇의 이동을 시뮬레이션하는 메서드로, steering(조향 각도)과 distance(이동 거리)를 인자로 받음
    def move(self, steering, distance,
        tolerance = 0.001, max_steering_angle = pi / 4.0):
				
				# max_steering_angle로 조향 각도 제한 
        if steering > max_steering_angle:
            steering = max_steering_angle
        if steering < -max_steering_angle:
            steering = -max_steering_angle
        if distance < 0.0:
            distance = 0.0
        
        # make a new copy
        res = robot()
        res.length    = self.length
        res.steering_noise = self.steering_noise
        res.distance_noise = self.distance_noise
        res.steering_drift = self.steering_drift

        # apply noise
        steering2 = random.gauss(steering, self.steering_noise)
        distance2 = random.gauss(distance, self.distance_noise)
        
        # apply steering drift
        steering2 += self.steering_drift
        
        # Execute motion
        turn = tan(steering2) * distance2 / res.length
        
				# 움직임 작으면 직선, 크면 원운동을 통해 이동 계산 
        if abs(turn) < tolerance:
            # approximate by straight line motion

            res.x = self.x + (distance2 * cos(self.orientation))
            res.y = self.y + (distance2 * sin(self.orientation))
            res.orientation = (self.orientation + turn) % (2.0 * pi)
        
        else:
            # approximate bicycle model for motion
            radius = distance2 / turn
            cx = self.x - (sin(self.orientation) * radius)
            cy = self.y + (cos(self.orientation) * radius)
            res.orientation = (self.orientation + turn) % (2.0 * pi)
            res.x = cx + (sin(res.orientation) * radius)
            res.y = cy - (cos(res.orientation) * radius)
        
        return res

    def __repr__(self):
        return '[x=%.5f y=%.5f orient=%.5f]' % (self.x, self.y, self.orientation)
    
# ------------------------------------------------------------------------
#
# run - does a single control run

# 로봇이 P-컨트롤러를 통해 경로를 따라가도록 제어하는 함수
def run(param):
    myrobot = robot()
    # 초기 위치 (x=0, y=1)
    myrobot.set(0.0, 1.0, 0.0)
    # speed를 1로 설정하여 이동 거리와 속도가 동일하도록 가정
    speed = 1.0 # motion distance is equal to speed (we assume time = 1)
    N = 100
    for _ in range(N):
        # steering angle = - tau * crosstrack_error
        # 조향 각도는 -param * myrobot.y로 설정
        # tau = param
        # myrobot.y는 y축에서의 편차(= crosstrack error)
        # 로봇이 y축에서 벗어났을 때 이를 조정해 다시 경로로 돌아오게 함
        steering_angle = -param*myrobot.y
        # 새로운 조향 각도와 속도를 이용해 로봇의 위치 업데이트 
        # 진동을 적절한 값으로 유지할 수 있도록! 
        myrobot = myrobot.move(steering_angle, speed)
        # 각 반복마다 로봇의 현재 위치와 조향 각도를 출력하여 이동 경로를 추적
        print(myrobot, steering_angle)
# y축 편차에 따라 조향 각도가 조정되는 것을 시뮬레이션
# 타우 = 0.1 
run(0.1) # call function with parameter tau of 0.1 and print results
```

```python
[x=1.00000 y=0.99749 orient=6.27817] -0.1
[x=1.99997 y=0.98997 orient=6.27316] -0.0997491638458655
[x=2.99989 y=0.97747 orient=6.26820] -0.09899729506124687
[x=3.99973 y=0.96003 orient=6.26330] -0.0977469440459231
[x=4.99948 y=0.93774 orient=6.25848] -0.0960031957433074
[x=5.99912 y=0.91068 orient=6.25378] -0.09377364753807171
[x=6.99861 y=0.87900 orient=6.24921] -0.09106837322063087
[x=7.99796 y=0.84283 orient=6.24481] -0.08789987335156867
[x=8.99714 y=0.80235 orient=6.24058] -0.08428301247961656
[x=9.99614 y=0.75775 orient=6.23656] -0.08023494377461873
[x=10.99497 y=0.70925 orient=6.23277] -0.07577502172916582
[x=11.99360 y=0.65707 orient=6.22921] -0.07092470365780627
[x=12.99206 y=0.60149 orient=6.22592] -0.06570744077966993
[x=13.99033 y=0.54275 orient=6.22291] -0.06014855970898907
[x=14.98843 y=0.48116 orient=6.22020] -0.05427513519858849
[x=15.98637 y=0.41701 orient=6.21779] -0.04811585498565592
[x=16.98416 y=0.35062 orient=6.21570] -0.04170087757827332
[x=17.98183 y=0.28231 orient=6.21395] -0.03506168379843757
[x=18.97938 y=0.21242 orient=6.21254] -0.028230922864679542
[x=19.97685 y=0.14130 orient=6.21147] -0.02124225375861215
[x=20.97428 y=0.06965 orient=6.21077] -0.014130182577480355
[x=21.97166 y=-0.00270 orient=6.21042] -0.006965132942895698
[x=22.96901 y=-0.07541 orient=6.21043] 0.00027038891366995137
[x=23.96637 y=-0.14810 orient=6.21081] 0.007540645276524681
[x=24.96375 y=-0.22041 orient=6.21155] 0.014809553271809207
[x=25.96122 y=-0.29143 orient=6.21265] 0.022040856550422525
[x=26.95879 y=-0.36118 orient=6.21411] 0.029143327380779738
[x=27.95647 y=-0.42930 orient=6.21592] 0.036118125383825375
[x=28.95428 y=-0.49545 orient=6.21806] 0.042930097792873316
[x=29.95224 y=-0.55929 orient=6.22054] 0.04954479013212563
[x=30.95036 y=-0.62049 orient=6.22334] 0.05592861665386977
[x=31.94866 y=-0.67875 orient=6.22645] 0.062049028300293685
[x=32.94715 y=-0.73376 orient=6.22985] 0.0678746774674778
[x=33.94582 y=-0.78523 orient=6.23352] 0.07337557879527594
[x=34.94468 y=-0.83291 orient=6.23746] 0.07852326515236428
[x=35.94373 y=-0.87654 orient=6.24163] 0.08329093793538789
[x=36.94296 y=-0.91588 orient=6.24603] 0.08765361075776071
[x=37.94235 y=-0.95074 orient=6.25062] 0.09158824557055995
[x=38.94189 y=-0.98092 orient=6.25539] 0.09507388023884573
[x=39.94157 y=-1.00625 orient=6.26031] 0.0980917465937722
[x=40.94136 y=-1.02661 orient=6.26535] 0.10062537799705923
[x=41.94124 y=-1.04186 orient=6.27051] 0.10266070549062078
[x=42.94119 y=-1.05193 orient=6.27573] 0.1041861416619156
[x=43.94118 y=-1.05674 orient=6.28101] 0.10519265143439327
[x=44.94118 y=-1.05626 orient=0.00313] 0.10567380909136831
[x=45.94116 y=-1.05048 orient=0.00843] 0.10562584095915782
[x=46.94110 y=-1.03941 orient=0.01370] 0.10504765330828719
[x=47.94096 y=-1.02310 orient=0.01892] 0.10394084517668034
[x=48.94073 y=-1.00161 orient=0.02405] 0.10230970597212946
[x=49.94038 y=-0.97505 orient=0.02908] 0.10016119786812681
[x=50.93988 y=-0.94353 orient=0.03397] 0.09750492316309477
[x=51.93922 y=-0.90720 orient=0.03870] 0.09435307692322965
[x=52.93838 y=-0.86624 orient=0.04325] 0.09072038536945969
[x=53.93735 y=-0.82084 orient=0.04759] 0.08662403059544772
[x=54.93611 y=-0.77121 orient=0.05170] 0.08208356231274366
[x=55.93467 y=-0.71760 orient=0.05557] 0.07712079740921354
[x=56.93303 y=-0.66026 orient=0.05916] 0.07175970817544908
[x=57.93118 y=-0.59948 orient=0.06247] 0.06602630010119129
[x=58.92913 y=-0.53556 orient=0.06547] 0.0599484801696292
[x=59.92690 y=-0.46880 orient=0.06815] 0.05355591658336607
[x=60.92450 y=-0.39953 orient=0.07050] 0.04687989084377478
[x=61.92195 y=-0.32810 orient=0.07249] 0.03995314307781542
[x=62.91926 y=-0.25485 orient=0.07414] 0.03280971146716638
[x=63.91646 y=-0.18014 orient=0.07541] 0.025484766586009757
[x=64.91362 y=-0.10481 orient=0.07631] 0.018014441401032855
[x=65.91071 y=-0.02857 orient=0.07684] 0.010480569580342894
[x=66.90776 y=0.04819 orient=0.07698] 0.002856874889775428
[x=67.90480 y=0.12509 orient=0.07674] -0.004819071006171856
[x=68.90186 y=0.20175 orient=0.07611] -0.012509259092914088
[x=69.89900 y=0.27729 orient=0.07510] -0.020175422770042084
[x=70.89623 y=0.35163 orient=0.07372] -0.02772891882232216
[x=71.89358 y=0.42440 orient=0.07196] -0.03516296594604001
[x=72.89107 y=0.49524 orient=0.06983] -0.042440155080760184
[x=73.88872 y=0.56378 orient=0.06736] -0.04952373709194831
[x=74.88654 y=0.62967 orient=0.06453] -0.05637780328932536
[x=75.88456 y=0.69259 orient=0.06138] -0.06296746381467529
[x=76.88278 y=0.75220 orient=0.05791] -0.06925902317998976
[x=77.88121 y=0.80820 orient=0.05414] -0.07522015217126068
[x=78.87986 y=0.86030 orient=0.05009] -0.08082005526579224
[x=79.87871 y=0.90822 orient=0.04578] -0.08602963264552842
[x=80.87776 y=0.95171 orient=0.04123] -0.09082163582916394
[x=81.87700 y=0.99054 orient=0.03646] -0.09517081589671933
[x=82.87643 y=1.02451 orient=0.03149] -0.099054063244742
[x=83.87601 y=1.05342 orient=0.02635] -0.10245053779285627
[x=84.87572 y=1.07712 orient=0.02106] -0.10534178856535165
[x=85.87555 y=1.09547 orient=0.01565] -0.10771186159780939
[x=86.87547 y=1.10838 orient=0.01015] -0.10954739516936059
[x=87.87544 y=1.11575 orient=0.00459] -0.11083770143698929
[x=88.87544 y=1.11754 orient=6.28217] -0.11157483364821985
[x=89.87543 y=1.11372 orient=6.27656] -0.11175363823138298
[x=90.87538 y=1.10430 orient=6.27097] -0.1113717912050447
[x=91.87527 y=1.08931 orient=6.26543] -0.11042981850702348
[x=92.87506 y=1.06882 orient=6.25996] -0.10893110001356945
[x=93.87472 y=1.04291 orient=6.25459] -0.1068818571960975
[x=94.87423 y=1.01171 orient=6.24936] -0.104291124540498
[x=95.87357 y=0.97535 orient=6.24428] -0.10117070502734862
[x=96.87272 y=0.93401 orient=6.23939] -0.0975351101346206
[x=97.87165 y=0.88790 orient=6.23471] -0.09340148497322787
[x=98.87037 y=0.83721 orient=6.23026] -0.08878951929553125
[x=99.86885 y=0.78221 orient=6.22606] -0.08372134522491592
```

⇒ p 측정기를 통해 y축에 대한 CTE 에러를 최소화 시키는 과정 

- run에 타우값을 0.3으로 바꿔서 넣으면 진동 속도에 영향이 있을 것
- 속도가 빠를수록 일관성을 어떻게 계속 이끌어 나갈 것인지 계속 실험을 통해 조정해야될 것
    
    → 나중엔 AI에서 이러한 부분들을 판단할 수 있도록 시스템을 모니터링하면서 해당 값을 보완해주는 형태로.. (지금까진 직접 테스트하고 최적의값 찾음) 
    
- 어떻게 해도 오버슛은 발생
    - `steering_angle`이 커졌다가 작아지는 형태로 반복되면, **오버슈팅과 진동**이 발생하고 있다는 것을 의미 → 로봇이 목표 경로를 지나쳐 다시 돌아오려는 현상
    - 오버슈팅은 주로 **비례 게인(τ)** 값이 너무 클 때 발생하며, `steering_angle`이 너무 민감하게 반응하면서 시스템이 불안정해지는 원인
    - 오버슈팅을 줄이려면 **비례 게인(τ)을 조정**해 `steering_angle`의 변화를 적당한 수준으로 낮춰야
        
        ![image 4](https://github.com/user-attachments/assets/ac0e0776-ac7a-41d0-a60f-c95d8b9ea8c1)
        
        ![image 5](https://github.com/user-attachments/assets/1af24daf-531b-437d-a830-30c4261450e2)
        
        ![image 6](https://github.com/user-attachments/assets/cd05f5ad-fbdc-4e11-8ca2-3c4c5a48b92a)
        
- P-컨트롤러는 본질적으로 오차에 비례해서만 반응하기 때문에, **항상 어느 정도의 진동**이 발생하는 특성을 가짐
- 비례 제어만 사용하면 목표 위치에 완벽하게 도달하지 않고, 남아있는 오차(steady-state error)를 줄이기 위해 계속해서 진동
- 진동을 줄이기 위해서는 **PD-컨트롤러**나 **PID-컨트롤러**를 사용하는 것이 효과적
- 사막이라면 이러한 이동이 괜찮을지 몰라도 자율주행에서 보통 이러면..

---

### PD-Controller

- 오버슛 최소화 진행 → 안정기 장착 “미분항 추가”: 시간에 의한 차이를 안정적으로 만듦
- 조향 알파는 게인 매개변수에 의한 CTE 오류와 관련이 있을뿐 아니라 CTE 의 시간적 파생과도 연결
    
    ![image 7](https://github.com/user-attachments/assets/ce5324a2-1094-4018-80e0-515a7916fa32)
    
    ![image 8](https://github.com/user-attachments/assets/3629995c-75df-4709-bb5c-815c8150fe6f)
    
    ⇒ diff_CTE = current_CTE - previous_CTE
    
    <aside>
    💡
    
    steer=−param1(tau_p)×CTE − param2(tau_d)×diff_CTE
    
    </aside>
    
    ⇒ PD 제어기는 현재 오차에 비례한 조향과 오차 변화율에 비례한 조향을 합산하여 조향 각도를 결정
    
    - `CTE (Cross Track Error)`: 로봇의 y 좌표 (목표 y 위치는 0)
    - `diff_CTE`: 이전 CTE와 현재 CTE의 차이. 미분 제어기의 입력으로 사용됨
- 차가 CTE를 줄일 수있을 만큼 충분히 회전 되었을때, x축에 대해서만 슈팅한것이 아니라, 오차를 줄일수 있다
- 미분은 시간 경과에따라 오차를 줄이고, 다시 위로 조향 가능케 함
- CTE를 만나도 삐끗하는게 아니라 항상 근접한 영역에서 지속적으로 갈 수 있게 하는데 이득값을 얻어온다면 전체 시스템 안정에 도움될 것
- 구조
    - **P(비례) 항**: P-컨트롤러처럼 현재 오차(CTE, Crosstrack Error)에 비례해 제어 값을 설정. 즉, 현재 로봇의 위치와 목표 경로 간의 차이를 바로 보정하려고 함
        - 타우p = 비례 게인
    - **D(미분) 항**: 오차의 **변화율**에 따라 제어 값을 조정. 오차가 줄어드는 속도가 클수록 미분 항이 작용하여 제어 값의 변화를 억제함으로써, **오버슈트를 줄이고 진동을 억제**.
        - 타우d = 미분 게인
        - 로봇이 지나치게 빠르게 움직여 목표를 지나치는 현상을 줄일 수 있음

```python
# 100번의 반복을 실행하여 PD 컨트롤러를 구현합니다.
# 로봇 모션. 조향 각도를 설정해야 합니다.
# 매개변수 tau를 사용하여 다음을 수행
#
# steering = -tau_p * CTE - tau_d * diff_CTE
# 차동 교차 추적 오류(diff_CTE)가 있다면 CTE(t) - CTE(t-1)로 제공

from math import *
import random

# ------------------------------------------------
#
# this is the robot class
#

class robot:
    # --------
    # init:
    # creates robot and initializes location/orientation to 0, 0, 0
    #

    def __init__(self, length = 20.0):
        self.x = 0.0
        self.y = 0.0
        self.orientation = 0.0

        self.length = length
        self.steering_noise = 0.0
        self.distance_noise = 0.0
        self.steering_drift = 0.0

    # --------
    # set:
    # sets a robot coordinate
    #

    def set(self, new_x, new_y, new_orientation):
        self.x = float(new_x)
        self.y = float(new_y)
        self.orientation = float(new_orientation) % (2.0 * pi)

    # --------
    # set_noise:
    # sets the noise parameters
    #

    def set_noise(self, new_s_noise, new_d_noise):
        # makes it possible to change the noise parameters
        # this is often useful in particle filters
        self.steering_noise = float(new_s_noise)
        self.distance_noise = float(new_d_noise)

    # --------
    # set_steering_drift:
    # sets the systematical steering drift parameter
    #

    def set_steering_drift(self, drift):
        self.steering_drift = drift

    # --------
    # move:
    # steering = front wheel steering angle, limited by max_steering_angle
    # distance = total distance driven, most be non-negative

    def move(self, steering, distance,
        tolerance = 0.001, max_steering_angle = pi / 4.0):

        if steering > max_steering_angle:
            steering = max_steering_angle
        if steering < -max_steering_angle:
            steering = -max_steering_angle
        if distance < 0.0:
            distance = 0.0

        # make a new copy
        res = robot()
        res.length    = self.length
        res.steering_noise = self.steering_noise
        res.distance_noise = self.distance_noise
        res.steering_drift = self.steering_drift

        # apply noise
        steering2 = random.gauss(steering, self.steering_noise)
        distance2 = random.gauss(distance, self.distance_noise)

        # apply steering drift
        steering2 += self.steering_drift

        # Execute motion
        turn = tan(steering2) * distance2 / res.length

        if abs(turn) < tolerance:
            # approximate by straight line motion

            res.x = self.x + (distance2 * cos(self.orientation))
            res.y = self.y + (distance2 * sin(self.orientation))
            res.orientation = (self.orientation + turn) % (2.0 * pi)
        else:
            # approximate bicycle model for motion

            radius = distance2 / turn
            cx = self.x - (sin(self.orientation) * radius)
            cy = self.y + (cos(self.orientation) * radius)
            res.orientation = (self.orientation + turn) % (2.0 * pi)
            res.x = cx + (sin(res.orientation) * radius)
            res.y = cy - (cos(res.orientation) * radius)
        return res

    def __repr__(self):
        return '[x=%.5f y=%.5f orient=%.5f]' % (self.x, self.y, self.orientation)
    
# ------------------------------------------------------------------------
#
# run - does a single control run.
def run(param1, param2):
    myrobot = robot()
    myrobot.set(0.0, 1.0, 0.0)
    #myrobot.set_steering_drift(10.0/180*pi)
    speed = 1.0 # motion distance is equal to speed (we assume time = 1)
    N = 100
    previous_CTE = myrobot.y
    for _ in range(N):
        current_CTE = myrobot.y
        diff_CTE = current_CTE - previous_CTE
        steer = -param1*myrobot.y - param2*diff_CTE
        myrobot = myrobot.move(steer, speed)
        previous_CTE = current_CTE
        print(myrobot, steer)

# Call your function with parameters of 0.2 and 3.0 and print results
# PD 제어기의 비례 및 미분 계수를 0.2와 3.0으로 설정
run(0.2, 3.0)
```

![image 9](https://github.com/user-attachments/assets/c2ffda30-52d5-40fc-9fd6-17e76abe40fa)

→ 0.2, 3.0

- 시간이 지남에 따라 CTE가 0에 가까워지며, 이는 로봇이 목표 경로에 근접해 가고 있다
    
    ↔ CTE가 **진동하거나 크게 변동**한다면, 이는 PD-컨트롤러가 불안정하거나 오버슈트가 발생하고 있다는 신호
    
- **CTE 그래프**는 PD-컨트롤러가 목표 경로에 얼마나 잘 수렴하고 있는지 보여줌
- 조향 각도(steering angle)가 급격하게 변하지 않고 매끄럽게 줄어드는 형태라면, PD-컨트롤러가 오차 변화율에 잘 반응하고 있다는 뜻

![image 10](https://github.com/user-attachments/assets/07309bc7-2dd6-4961-ba09-6f92505fce0f)

→ 0.2, 0.0 = 미분계수 0 = 비례제어기만 활성화 

- 로봇은 현재의 오차에만 의존하여 조향 각도를 결정하게 됨
- 과거의 오차 변화에 대한 정보를 사용하지 않으므로, 로봇이 경로를 따라 움직일 때 미세 조정이 이루어지지 않음
- 이 경우 로봇은 목표 경로로 가기 위해 조향 각도를 즉각적으로 조정하지만, 오차가 줄어들거나 커지는 속도를 고려하지 않기 때문에, 만약 로봇이 목표 경로에서 이탈할 경우 과도한 진동이 발생
- 즉, 로봇이 목표 경로로 돌아오기 위해 큰 조향 각도를 계속 사용하게 되어 제어가 불안정해질 수 있음

---

### PID-Controller

- 차량 정비 실수로 바퀴가 10도 정도 비스듬히 정렬된 상황 ⇒ CTE 문제 발생
- 시간의 흐름에 따라 편향에 대한 공간들 존재
- 편향(+10도, -10도 만큼 ; 시스템적 드리프트)에 대한 면적.. 에러율 다 합쳐서 적용
    
    integral += error * dt
    
    ⇒ 적분은 기본적으로 '면적을 구하는 것'과 비슷. CTE가 일정 시간 동안 존재하면, 그 면적을 계산해서 에러를 누적하는 것
    
    ![image 11](https://github.com/user-attachments/assets/98022c4a-1277-44a7-a899-3a621dfd20e7)
    
    ⇒ 로봇이 경로를 따라가더라도 목표 경로에서 벗어나는 경우 발생 
    
- 적분기 추가
    
    ![image 12](https://github.com/user-attachments/assets/f1e20b28-8cdf-49f3-afd2-856bfc177ec3)
    
    ⇒ 스티어링 드리프트 상쇄 
    
- 구조
    - τp (비례 항): 현재 CTE에 비례하여 조향을 조정.
    - τd (미분 항): CTE의 변화율(즉, 이전 CTE와의 차이에 기반하여) 조향을 조정.
    - τi (적분 항): 이전 모든 CTE의 합계로, 조향 드리프트를 보정하는 데 사용.

```python
# 100번의 반복을 실행하여 PID 컨트롤러를 구현합니다.
# 로봇 모션. 조향 각도를 설정해야 합니다.
# 매개변수 tau를 사용하여 다음을 수행합니다.
#
# 스티어링 = -tau_p * CTE - tau_d * diff_CTE - tau_i * int_CTE
#
# 통합 교차 추적 오류(int_CTE)는 다음과 같습니다.
# 이전의 모든 크로스트랙 오류의 합계입니다.
# 이 용어는 스티어링 드리프트를 상쇄하는 데 사용됩니다.

from math import *
import random

# ------------------------------------------------
#
# this is the robot class
#

class robot:

    # --------
    # init:
    # creates robot and initializes location/orientation to 0, 0, 0
    #

    def __init__(self, length = 20.0):
        self.x = 0.0
        self.y = 0.0
        self.orientation = 0.0

        self.length = length
        self.steering_noise = 0.0
        self.distance_noise = 0.0
        self.steering_drift = 0.0
        
    # --------
    # set:
    #    sets a robot coordinate
    #

    def set(self, new_x, new_y, new_orientation):
        self.x = float(new_x)
        self.y = float(new_y)
        self.orientation = float(new_orientation) % (2.0 * pi)

    # --------
    # set_noise:
    #    sets the noise parameters
    #

    def set_noise(self, new_s_noise, new_d_noise):
        # makes it possible to change the noise parameters
        # this is often useful in particle filters
        self.steering_noise = float(new_s_noise)
        self.distance_noise = float(new_d_noise)

    # --------
    # set_steering_drift:
    #    sets the systematical steering drift parameter
    #

    def set_steering_drift(self, drift):
        self.steering_drift = drift

    # --------
    # move:
    # steering = front wheel steering angle, limited by max_steering_angle
    # distance = total distance driven, most be non-negative

    def move(self, steering, distance, tolerance = 0.001, max_steering_angle = pi / 4.0):
        if steering > max_steering_angle:
            steering = max_steering_angle
        if steering < -max_steering_angle:
            steering = -max_steering_angle
        if distance < 0.0:
            distance = 0.0

        # make a new copy
        res = robot()
        res.length    = self.length
        res.steering_noise = self.steering_noise
        res.distance_noise = self.distance_noise
        res.steering_drift = self.steering_drift

        # apply noise
        steering2 = random.gauss(steering, self.steering_noise)
        distance2 = random.gauss(distance, self.distance_noise)

        # apply steering drift
        steering2 += self.steering_drift
        
        # Execute motion
        turn = tan(steering2) * distance2 / res.length
        
        if abs(turn) < tolerance:
            # approximate by straight line motion
            res.x = self.x + (distance2 * cos(self.orientation))
            res.y = self.y + (distance2 * sin(self.orientation))
            res.orientation = (self.orientation + turn) % (2.0 * pi)

        else:
            # approximate bicycle model for motion
            radius = distance2 / turn
            cx = self.x - (sin(self.orientation) * radius)
            cy = self.y + (cos(self.orientation) * radius)
            res.orientation = (self.orientation + turn) % (2.0 * pi)
            res.x = cx + (sin(res.orientation) * radius)
            res.y = cy - (cos(res.orientation) * radius)
        
        return res

    def __repr__(self):
        return '[x=%.5f y=%.5f orient=%.5f]' % (self.x, self.y, self.orientation)

# ------------------------------------------------------------------------
#
# run - does a single control run.

def run(param1, param2, param3):
    myrobot = robot()
    myrobot.set(0.0, 1.0, 0.0)
    speed = 1.0 # motion distance is equal to speed (we assume time = 1)
    N = 100
    # 10 degree bias, this will be added in by the move function, you do not need to add it below!
    # 바퀴 10도 편향
    myrobot.set_steering_drift(10.0 / 180.0 * pi) 
    previous_CTE = myrobot.y
    int_CTE = 0.0

    for i in range(N):
        current_CTE = myrobot.y
        # 미분기 
        diff_CTE = current_CTE - previous_CTE
        # 적분기 (누적시켜줌)
        int_CTE += current_CTE
        # 최종값 
        # control = Kp * error + Ki * integral + Kd * derivative
        steer = -param1*current_CTE - param2*diff_CTE - param3*int_CTE
        myrobot = myrobot.move(steer, speed)
        previous_CTE = current_CTE
        print (myrobot, steer)

# Call your function with parameters of (0.2, 3.0, and 0.004)
# 적분기 추가됨 
run(0.2, 3.0, 0.004)
```

![image 13](https://github.com/user-attachments/assets/6788eeca-29a8-44b1-839f-4f2d63cfd5ef)

1. CTE
    - CTE 값은 로봇의 현재 위치와 목표 경로(직선) 간의 수직 거리. 목표 경로에 잘 따라가고 있다면 CTE는 0에 수렴해야 함. 즉, 로봇이 목표 경로를 벗어나지 않고 그 위에 있는 경우, CTE는 0이 됨.
    - CTE가 0에 가까워질수록 로봇이 경로를 잘 추적하고 있다는 것을 의미.
2. 조향 각도 
    - 조향 각도는 로봇이 목표 경로를 따라가기 위해 필요한 조정. 직선을 따라 이동할 때 조향 각도는 일반적으로 0에 수렴해야 함. 즉, 로봇이 직선 경로를 따를 때 조향이 필요 없거나 매우 작아야 함.
    - 조향 각도가 0에 가까워질수록 로봇이 직선을 따라 안정적으로 이동하고 있다는 것을 나타냄.
3. 위치
    - 그래프에서 로봇의 경로가 직선 경로와 일치하면 로봇이 잘 움직이고 있다는 것을 의미.
    - 시간이 지남에 따라 CTE와 조향 각도가 안정적이고 작은 값을 유지하면 로봇이 경로를 잘 따라가고 있다는 것을 의미.

⇒ CTE 그래프가 시간에 따라 0으로 수렴하는 모습을 보여야 하며, 진동이 적고 안정적인 상태여야 함 & 조향 각도가 0에 가까워지는 모습을 보여야 하며, 과도한 조정이 없다면 그래프가 안정적으로 0 근처에서 유지되어야 함 

- Q. 조향각도를 0으로 주면?
    
    ![image 14](https://github.com/user-attachments/assets/2ddc5424-b387-4615-804e-c25d135a1736)
    
    ⇒ 로봇이 경로를 따라가더라도 목표 경로에서 벗어나는 경우 발생
