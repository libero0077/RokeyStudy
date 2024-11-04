# 액션 프로그래밍
## 비교
### - 토픽 프로그래밍
데이터나 이벤트를 주제로 구독 모델로 전달하는 방식, MQTT, Kafka 같은 메시징 시스템에서 주로 사용

### - 서비스 프로그래밍
독립된 기능을 서비스로 분리하여 여러 시스템에서 사용할 수 있도록 API 형태로 제공. 요청과 동기적 통신이 토픽과 구별되는 차이점. 주로 REST API나 gRPC로 구현됨.

### - 액션 프로그래밍
특정 조건이나 이벤트가 발생했을 때 실행되는 '행동' 중심의 프로그래밍 패러다임   
![image](https://github.com/user-attachments/assets/1177f0f3-cbcd-4c3f-9077-6e636f524eaa)

## 액션 프로그래밍의 구성 요소
### - event
액션을 트리거하는 특정 조건이나 상태 변화
### - action
이벤트가 발생했을 때 수행될 작업
### - action state
액션이 실행되는 환경이나 상태

## 예시(스마트 홈 시스템)
### - 토픽
여러 센서가 데이터를 발행함. 구독자는 실시간으로 이를 모니터링

### - 서비스
사용자가 앱을 통해 조명을 제어를 요청, 서버에서 처리하여 응답함
### - 액션 프로그래밍
모션 센서가 감지되면 자동으로 조명이 켜짐   
![image](https://github.com/user-attachments/assets/ffa768a4-8d65-496f-9edc-fec828993750)
## 다시 비교해보기
![image](https://github.com/user-attachments/assets/e63cf949-a14c-470e-abcb-8e5b7eaa4b50)

## 순서 파악해보기
![F4302_1](https://github.com/user-attachments/assets/0dfa0852-706f-430a-8f26-fef677f61747)

## 코드 분석
### calculator.py
```python
    self.arithmetic_action_server = ActionServer( # 액션 서버, rclpy.action 모듈의 ActionServer 클래스
        self,
        ArithmeticChecker,    # 액션의 타입
        'arithmetic_checker', # 액션 이름
        self.execute_checker, # 액션 목표를 받으면 실행되는 콜백함수
        callback_group=self.callback_group) # 멀티 스레드 병렬 콜백 함수 실행을 위한 설정
```
```python
    def execute_checker(self, goal_handle): # goal_handle 매개변수는 rclpy.action 모듈의 ServerGaolHandle 클래스로 생성된 액션 상태 처리용
                                            # execute, succeed, abort, canceled 등과 같이 액션 상태에 따른 관련 함수 호출이 가능, publish_feedback 가능
        self.get_logger().info('Execute arithmetic_checker action!')  # 액션 서버 시작
        feedback_msg = ArithmeticChecker.Feedback() # 액션 피드백을 보낼 변수
        feedback_msg.formula = []                   # 실제 피드백
        total_sum = 0.0                             # 실제 피드백
        goal_sum = goal_handle.request.goal_sum     # 액션 목표값
```
```python
        while total_sum < goal_sum: # 목표값(goal_sum)과 누적값을 비교
            total_sum += self.argument_result # 누적
            feedback_msg.formula.append(self.argument_formula) # 연산식을 저장?
            self.get_logger().info('Feedback: {0}'.format(feedback_msg.formula)) # 디버깅을 위한 송출
            goal_handle.publish_feedback(feedback_msg)
            time.sleep(1)
```
```python
        goal_handle.succeed() # 액션 목표를 달성한 상태로 전환하는 함수
        result = ArithmeticChecker.Result()
        result.all_formula = feedback_msg.formula # 전체 계산식을 저장
        result.total_sum = total_sum # 연산 합계를 저장
        return result # 결과값 리턴
```
### Checker.py
```python
class Checker(Node): # Node 클래스 상속

    def __init__(self):
        super().__init__('checker') # checker 라는 노드로 초기화
        self.arithmetic_action_client = ActionClient( # 액션 클라이언트 선언
          self,
          ArithmeticChecker, # 액션의 타입
          'arithmetic_checker') # 액션의 이름

    def send_goal_total_sum(self, goal_sum):
        wait_count = 1 # 연결에 문제가 있을 때 while문 반복
        while not self.arithmetic_action_client.wait_for_server(timeout_sec=0.1):
            if wait_count > 3:
                self.get_logger().warning('Arithmetic action server is not available.')
                return False
            wait_count += 1
        goal_msg = ArithmeticChecker.Goal() # 액션 메시지 선언
        goal_msg.goal_sum = (float)(goal_sum) # 액션 목표값 설정
        self.send_goal_future = self.arithmetic_action_client.send_goal_async( # 액션 메시지를 매개변수로 전달
            goal_msg,
            feedback_callback=self.get_arithmetic_action_feedback) # 액션 피드백을 받는 콜백함수
        self.send_goal_future.add_done_callback(self.get_arithmetic_action_goal) # 비동기 future task인 액션 목표값 전달
        return True

    def get_arithmetic_action_goal(self, future): # 액션 상태값 콜백 함수, 액션 서버가 목표값을 전달받고 accepted 상태일 때를 확인하여 처리
        goal_handle = future.result()
        if not goal_handle.accepted:
            self.get_logger().warning('Action goal rejected.')
            return
        self.get_logger().info('Action goal accepted.')
        self.action_result_future = goal_handle.get_result_async()
        self.action_result_future.add_done_callback(self.get_arithmetic_action_result)

    def get_arithmetic_action_feedback(self, feedback_msg):
        action_feedback = feedback_msg.feedback.formula
        self.get_logger().info('Action feedback: {0}'.format(action_feedback)) # 로그 출력

    def get_arithmetic_action_result(self, future): # 액션 결과값 콜백 함수
        action_status = future.result().status      # 현재 상태값과 결과값을 받고, 상태값이 조건을 만족하면 출력
        action_result = future.result().result
        if action_status == GoalStatus.STATUS_SUCCEEDED:
            self.get_logger().info('Action succeeded!')
            self.get_logger().info(
                'Action result(all formula): {0}'.format(action_result.all_formula))
            self.get_logger().info(
                'Action result(total sum): {0}'.format(action_result.total_sum))
        else:
            self.get_logger().warning(
                'Action failed with status: {0}'.format(action_status))
```
