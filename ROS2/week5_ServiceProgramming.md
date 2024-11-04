서비스 노드
==================
서비스(Service)는 아래 그림의 `Node B - Node C`처럼 동기식 양방향 메시지 송수신 방식으로 서비스의 요청(Request)을 하는 쪽을 Service client라고 하며 서비스의 응답(Response)을 하는 쪽을 Service server라고 한다. 결국 서비스는 특정 요청을 하는 클라이언트 단과 요청받은 일을 수행 후에 결과 값을 전달하는 서버 단과의 통신이라고 볼 수 있다. 서비스 요청 및 응답(Request/Response) 또한 위에서 언급한 msg 메시지의 변형으로 srv 메시지라고 한다.

![service](https://github.com/pedrala/DeepLearningStudy/blob/main/img/service.png?raw=true)


기본 구조
==================
![serviceDiagram](https://github.com/pedrala/DeepLearningStudy/blob/main/img/serviceDiagram.png?raw=true)


1:N 구조
==================

서비스는 다음 그림과 같이 동일 서비스에 대해 복수의 클라이언트를 가질 수 있도록 설계되었다. 단, 서비스 응답은 서비스 요청이 있었던 서비스 클라이언트에 대해서만 응답을 하는 형태로 그림의 구성에서 예를 들자면 Node C의 Service Client가 Node B의 Service Server에게 서비스 요청을 하였다면 Node B의 Service Server는 요청받은 서비스를 수행한 후 Node C의 Service Client에게만 서비스 응답을 하게된다.
![serviceServer_client](https://github.com/pedrala/DeepLearningStudy/blob/main/img/serviceServer_client.png?raw=true)


ex_calculator 패키지 구조
==================

서비스 요청을 하는 서비스 클라이언트와 서비스 응답을 하는 서비스 서버를 작성해 볼 것이다. 여기서 서비스 요청 값으로는 연산자(+, -, *, /)를 임의로 선택 후에 보낼 것이고 기존에 저장한 변수 a, b를 요청 값으로 받은 연산자로 계산하여 결괏값을 서비스 응답 값으로 보내는 프로그램을 짜볼 것이다. 강좌 진행에 앞서서 서비스에 대한 자세한 내용은 ROS 2 서비스 (service)' 강좌를 참고하도록 하자.

![client_server](https://github.com/pedrala/DeepLearningStudy/blob/main/img/client_server.png?raw=true)


​operator node
------------
arithmetic_operator 이라는 서비스 이름으로 calculator 노드에게 연산자(+, -, *, /)를 서비스 요청(Request)값으로 보낸다.

calculator node
------------
서브스크라이브하여 저장하고 있는 변수 a와 b와 operator 노드로부터 요청 값으로 받은 연산자를 이용하여 계산(a 연산자 b)하고 operator 노드에게 연산의 결괏값을 서비스 응답(Response)값으로 보낸다.




ex_calculator 패키지 소스코드 분석
==================


operator.py
------------

Operator 클래스이다. rclpy.node 모듈의 Node 클래스를 상속하고 있으며 생성자에서 'operator' 이라는 노드 이름으로 초기화되었다. 

그 뒤 arithmetic_service_client이라는 이름으로 서비스 클라이언트를 선언해주는데 이는 Node 클래스의 create_client 함수를 이용하여 서비스 클라이언트로 선언하는 부분으로 서비스의 타입으로 서비스 서버와 동일하게 ArithmeticOperator으로 선언하였고, 서비스 이름으로는 'arithmetic_operator'으로 선언하였다.

arithmetic_service_client 의 wait_for_service 함수는 서비스 요청을 할 수 있는 상태인지 알아보기 위해 서비스 서버가 실행되어 있는지 확인하는 함수로 0.1초 간격으로 서비스 서버가 실행되어 있는지 확인하게 된다.
```python

class Operator(Node):

    def __init__(self):
        super().__init__('operator')  # 'operator'라는 이름으로 노드 초기화

        # ArithmeticOperator 서비스를 위한 클라이언트 생성
        self.arithmetic_service_client = self.create_client(
            ArithmeticOperator,  # 사용할 서비스 타입
            'arithmetic_operator')  # 서비스 이름

        # 서비스가 준비될 때까지 기다리는 루프
        while not self.arithmetic_service_client.wait_for_service(timeout_sec=0.1):
            self.get_logger().warning('The arithmetic_operator service not available.')  # 서비스가 준비되지 않으면 경고 로그 출력
```
우리가 작성하고 있는 서비스 클라이언트의 목적은 서비스 서버에게 연산에 필요한 연산자를 보내는 것이라고 했다. 

이 send_request 함수가 실질적인 서비스 클라이언트의 실행 코드로 서비스 서버에게 서비스 요청값을 보내고 응답값을 받게 된다. 

서비스 요청값을 보내기 위하여 제일 먼저 우리가 미리 작성해둔 서비스 인터페이스 ArithmeticOperator.Request() 클래스로 service_request를 선언하였고 서비스 요청값으로 random.randint() 함수를 이용하여 특정 연산자를 self.request의 arithmetic_operator 변수에 저장하였다.

그 뒤 'call_async(self.request)' 함수로 서비스 요청을 수행하게 설정하였다. 

끝으로 서비스 상태 및 응답값을 담은 futures를 반환하게 된다.

ROS 2에서 서비스 클라이언트는 동기식 및 비동기식으로 작동할 수 있다. 주어진 코드에서는 비동기식 (call_async)을 사용하고 있다. 

서비스가 동기식으로 작동한다는 것은 서비스 클라이언트가 요청을 보내고 응답을 기다리는 동안 블록(block)할 수 있다는 것을 의미한다. 

비동기식으로 요청을 보내면, 클라이언트는 요청을 보낸 후에 블록하지 않고, 나중에 응답을 받을 수 있다.

동기 Request인 call()도 있지만, 이렇게 되는 경우 다른 callback의 사용, 혹은 spin의 동작을 막게 되어 전체 시스템의 deadlock을 유발합니다. 

때문에 공식 튜토리얼에서도 비동기 Call을 추천하고 있습니다.

```python


    # 서비스 요청을 보내는 함수
    def send_request(self):
        service_request = ArithmeticOperator.Request()  # 서비스 요청 메시지 생성
        service_request.arithmetic_operator = random.randint(1, 4)  # 랜덤하게 1부터 4까지의 연산자 선택 (예: 더하기, 빼기, 곱하기, 나누기)
        futures = self.arithmetic_service_client.call_async(service_request)  # 비동기로 서비스 호출
        return futures  # 서비스 응답을 대기하는 future 객체 반환

```


setup.py
------------

서비스 클라이언트 노드인 operator 노드는 'ex_calculator' 패키지의 일부로 패키지 설정 파일에 'entry_points'로 실행 가능한 콘솔 스크립트의 이름과 호출 함수를 기입하도록 되어 있는데 우리는 하기와 같이 4개의 노드를 작성하고 'ros2 run' 과 같은 노드 실행 명령어를 통하여 각각의 노드를 실행시키고 있다. 

operator 노드는 ex_calculator 패키지의 arithmetic 폴더에 operator.py의 main문에 실행 코드가 담겨져 있다.
```python

  entry_points={
        'console_scripts': [
            'argument = ex_calculator.arithmetic.argument:main',
            'operator = ex_calculator.arithmetic.operator:main',
            'calculator = ex_calculator.calculator.main:main',
            'checker = ex_calculator.checker.main:main',
        ],
    },

```


operator.py
------------
​즉, 다음이 main함수가 실행 코드인데 rclpy.init를 이용하여 초기화하고 위에서 작성한 Operator 클래스를 operator라는 이름으로 생성한 다음 future = operator.send_request() 와 같이 서비스 요청을 보내고 응답값을 받게된다. 

그 뒤 rclpy.spin_once 함수를 이용하여 생성한 노드를 주기적으로 spin시켜 지정된 콜백함수가 실행될 수 있도록 하고 있다. 

이때 매 spin마다 노드의 콜백함수가 실행되고 서비스 응답값을 받았을 때 future의 done 함수를 이용해 요청값을 제대로 받았는지 확인 후 결괏값은 service_response = future.result() 같이 service_response라는 변수에 저장하여 사용하게 된다.

서비스 응답값은 get_logger().info() 함수를 이용하여 화면에 서비스 응답값에 해당되는 연산 결괏값을 표시하는 것이다. 

그리고 종료 `Ctrl + c`와 같은 인터럽트 시그널 예외 상황에서는 operator를 소멸시키고 rclpy.shutdown 함수로 노드를 종료하게 된다.

```python


# 메인 함수 정의: 노드를 실행하고 서비스 요청을 보내고 응답을 처리
def main(args=None):
    rclpy.init(args=args)  # rclpy 초기화
    operator = Operator()  # Operator 클래스 인스턴스 생성
    future = operator.send_request()  # 첫 번째 서비스 요청 보내기
    user_trigger = True  # 사용자 트리거 변수로, 사용자가 새로운 요청을 할 준비가 되었는지 관리

    try:
        while rclpy.ok():  # rclpy가 정상적으로 실행되는 동안 루프를 계속 돌림
            if user_trigger is True:  # 사용자가 요청을 할 준비가 되었을 때
                rclpy.spin_once(operator)  # 노드의 콜백 함수들을 한 번만 실행 (블록하지 않음)
                if future.done():  # future가 완료되었는지 확인
                    try:
                        service_response = future.result()  # 서비스 응답 결과를 받음
                    except Exception as e:  # 서비스 호출에 실패한 경우 예외 처리
                        operator.get_logger().warn('Service call failed: {}'.format(str(e)))  # 오류 메시지 출력
                    else:
                        operator.get_logger().info(
                            'Result: {}'.format(service_response.arithmetic_result))  # 성공하면 결과 출력
                        user_trigger = False  # 다음 입력을 기다리기 위해 트리거를 False로 변경
            else:
                input('Press Enter for next service call.')  # 다음 서비스 요청을 위해 Enter 입력 대기
                future = operator.send_request()  # 새 요청 보내기
                user_trigger = True  # 트리거를 True로 설정하여 요청 준비 완료

    except KeyboardInterrupt:  # 사용자가 Ctrl+C로 프로그램을 종료할 때
        operator.get_logger().info('Keyboard Interrupt (SIGINT)')  # 로그에 인터럽트 메시지 출력

    operator.destroy_node()  # 노드 종료
    rclpy.shutdown()  # rclpy 종료

# 만약 이 스크립트가 메인 스크립트로 실행되면 main() 함수 실행
if __name__ == '__main__':
    main()

```

calculator.py
------------
calculator 노드는 서브스크라이브하여 저장하고 있는 변수 a와 b와 operator 노드로부터 요청 값으로 받은 연산자를 이용하여 계산(a 연산자 b)하고 operator 노드에게 연산의 결괏값을 서비스 응답값으로 보낸다.

이 중 서비스 서버와 관련한 코드는 아래와 같다. 서버 관련 코드는 서비스 서버로 선언하는 부분과 콜백함수를 지정하는 것이다. arithmetic_service_server이 서비스 서버로 이는 Node 클래스의 create_service 함수를 이용하여 서비스 서버로 선언되었으며 서비스의 타입으로 ArithmeticOperator으로 선언하였고, 서비스 이름으로는 'arithmetic_operator', 서비스 클라이언트로부터 서비스 요청이 있으면 실행되는 콜백함수는 get_arithmetic_operator 으로 지정했으며 멀티 스레드 병렬 콜백함수 실행을 위해 지난번 강좌에서 설명한 callback_group 설정을 하였다. 

이러한 설정들은 서비스 서버를 위한 기본 설정이고 실제 서비스 요청에 해당되는 특정 수행 코드가 수행되는 부분은 get_arithmetic_operator 이라는 콜백함수임을 알아두자.

```python

        # ArithmeticOperator 서비스를 제공
        self.arithmetic_service_server = self.create_service(
            ArithmeticOperator,  # 제공할 서비스 타입
            'arithmetic_operator',  # 서비스 이름
            self.get_arithmetic_operator,  # 서비스 호출 시 실행될 콜백 함수
            callback_group=self.callback_group)  # 콜백 그룹 사용
```
            
get_arithmetic_operator 함수의 내용을 보도록 하자. 우선 제일 먼저 request와 response 이라는 매개 변수가 보이는데 이는 ArithmeticOperator() 클래스로 생성된 인터페이스로 서비스 요청에 해당되는 request 부분과 응답에 해당되는 response으로 구분된다. 

get_arithmetic_operator 함수는 서비스 요청이 있을 때 실행되는 콜백함수인데 여기서는 서비스 요청시 요청값으로 받은 연산자와 이전에 토픽 서브스크라이버가 토픽 값으로 전달받아 저장해둔 변수 a, b를 전달받은 연산자로 연산 후에 결괏값을 서비스 응답값으로 반환한다. 

이 함수의 첫 줄에서 request.arithmetic_operator를 받아와서 calculate_given_formula 함수에서 서비스 요청값으로 받은 연산자에 따라 연산하는 코드를 볼 수 있을 것이다. calculate_given_formula 함수로부터 받은 연산 결괏값은 response.arithmetic_result에 저장하고 끝으로 관련 수식을 문자열로 표현하여 get_logger().info() 함수를 통해 화면에 표시하고 있다. 

```python
    # 서비스 요청을 처리하는 콜백 함수
    def get_arithmetic_operator(self, request, response):
        self.argument_operator = request.arithmetic_operator  # 서비스로 받은 연산자 값 저장
        # 계산 결과를 계산하는 함수 호출
        self.argument_result = self.calculate_given_formula(
            self.argument_a,  # 첫 번째 값
            self.argument_b,  # 두 번째 값
            self.argument_operator)  # 연산자 값
        # 서비스 응답에 계산 결과 저장
        response.arithmetic_result = self.argument_result
        # 연산 공식을 문자열로 저장
        self.argument_formula = '{0} {1} {2} = {3}'.format(
            self.argument_a,
            self.operator[self.argument_operator - 1],  # 연산자 기호를 리스트에서 가져옴
            self.argument_b,
            self.argument_result)
        # 계산 결과 공식 로그 출력
        self.get_logger().info(self.argument_formula)
        return response  # 서비스 응답 반환

```


calculate_given_formula 함수로 인수 a, b를 가지고 주어진 연산자 operator에 따라 사칙연산을 하여 결괏값을 반환한다.

```python

    # 연산을 수행하는 함수
    def calculate_given_formula(self, a, b, operator):
        if operator == ArithmeticOperator.Request.PLUS:
            self.argument_result = a + b  # 더하기
        elif operator == ArithmeticOperator.Request.MINUS:
            self.argument_result = a - b  # 빼기
        elif operator == ArithmeticOperator.Request.MULTIPLY:
            self.argument_result = a * b  # 곱하기
        elif operator == ArithmeticOperator.Request.DIVISION:
            try:
                self.argument_result = a / b  # 나누기
            except ZeroDivisionError:
                self.get_logger().error('ZeroDivisionError!')  # 0으로 나눌 때 오류 로그 출력
                self.argument_result = 0.0  # 오류 발생 시 결과를 0으로 설정
                return self.argument_result
        else:
            self.get_logger().error('Please make sure arithmetic operator(plus, minus, multiply, division).')  # 잘못된 연산자 오류 로그
            self.argument_result = 0.0  # 오류 발생 시 결과를 0으로 설정
        return self.argument_result  # 연산 결과 반환

```

서비스 프로그래밍 참고자료
------------------------

https://rb2023ros.github.io/kr/ros2_foxy/lecture8/
