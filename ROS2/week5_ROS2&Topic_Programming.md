## #1 ROS2 Intro And Topic

## 1. ROS Intro

### - ROS란?
ROS(Robot Operating System) 는 오픈 소스이면서 로봇의 위한 메타(Meta) OS이다.   
소프트웨어 프레임워크 추가적으로 미들웨어라고 설명할 수 있음    
다양한 사람들과 협업을 하기 위해서는 여러 디바이스(펌웨어)를 컨트롤을 하기위해서는  
오픈 소스 아키텍쳐가 필요한데 그 부분이 미들웨어이다.  

기존의 로봇 개발 방식은 하드웨어 설계부터 소프트웨어에 이르기까지 모든 것을 독자적으로   개발해야 했기때문에 로봇마다 API 인터페이스가 다르고 이를 적용하는데 사전 학습이 필요했으며, 소프트웨어를 작성하는데 하드웨어에 대한 지식이 필요했습니다.   
이런 한계를 극복하여 로봇 프로그래밍 생태계를 구축하고자 많은 종류의 로봇 소프트웨어 플랫폼이 생겼는데 그 중 하나가 ROS이다.    


### - Meta OS란?
메타 운영체제(Meta-operating system)는 응용 프로그램 개발을 위한 시스템으로, 스케줄링, 로드, 모니터링, 오류 처리 등의 기능을 제공한다.   
즉, 메타 운영체제는 전통적인 운영체제와 달리 하드웨어 자원 관리를 넘어서, 특정 목적을 가진 응용 프로그램 개발에 최적화된 환경을 조성하는 것이 특징이다.  

ROS(로봇 운영체제, Robot Operating System)는 대표적인 메타 운영체제로, 로봇 응용 프로그램 개발을 지원하는 플랫폼이다.   
ROS는 리눅스, 윈도우, Mac OS, 안드로이드와 같은 전통적인 운영체제가 아니라, 이러한 기존 OS를 기반으로 동작하며 그 위에 로봇 개발을 위한 다양한 기능을 제공한다.   
ROS는 미들웨어, 소프트웨어 프레임워크, 도구 세트, 통신 인프라, 그리고 에코시스템을 결합한 형태로, 이를 통해 개발자는 복잡한 로봇 시스템을 보다 쉽게 구축하고 관리할 수 있다.  

ROS는 기존 운영체제의 스케줄링, 파일 시스템, 프로세스 관리 등의 기능을 활용하면서, 로봇 개발에 필요한 추가적인 기능을 제공하여 메타 운영체제의 역할을 수행한다.  
이를 통해 개발자들은 하드웨어에 종속되지 않고, 다양한 로봇 시스템에 범용적으로 적용 가능한 소프트웨어를 개발할 수 있다.  

메타 운영체제의 특징으로 **추상화**와 **확장성**을 언급할 수 있다.   
메타 운영체제는 기본적인 시스템 자원 관리를 추상화하고, 이를 통해 다양한 하드웨어 환경에서 일관된 개발 경험을 제공하며, 특정 응용 분야에 맞게 확장 가능하다는 점이 중요한 장점이다.  

1. 추상화는 복잡한 하드웨어나 소프트웨어의 세부 사항을 숨기고, 개발자가 보다 단순한 인터페이스를 통해 시스템을 사용할 수 있도록 만드는 과정이다. 메타 운영체제에서 추상화는 하드웨어의 복잡성을 숨기고, 일관된 API나 인터페이스를 제공하는 것을 의미

예시: ROS에서, 개발자는 로봇의 각기 다른 센서나 모터를 직접 제어하는 대신, ROS가 제공하는 메시지, 토픽, 서비스 등의 개념을 사용하여 통신하고 데이터를 주고받을 수 있음 이렇게 함으로써 개발자는 다양한 하드웨어에 종속되지 않고, 추상화된 방식으로 로봇을 제어할 수 있다.

2. 확장성은 시스템이 변화하는 요구나 환경에 맞춰 기능을 쉽게 확장할 수 있는 능력을 의미 메타 운영체제는 새로운 기능이나 하드웨어를 추가할 때 기존 시스템을 크게 변경하지 않고도 시스템을 확장할 수 있는 구조를 제공한다.

예시: ROS는 모듈형 아키텍처로 설계되어 있어, 새로운 노드(프로그램)를 추가하거나 기존의 노드를 변경하여 기능을 확장할 수 있다.   
예를 들어, 로봇에 새로운 센서를 장착하면 해당 센서에 맞는 노드를 추가하는 것만으로도 시스템을 쉽게 확장할 수 있음  

### - ROS2 Message 통신(DDS)
DDS는 Data Distribution System의 약자이다. 한글로 하면 **데이터 분산 서비스**라고 한다.  
DDS는 publish-subscribe pattern을 바탕으로 높은 실시간성/신뢰성/퍼포먼스(빠른 속도)등을 목표로 하고 있고 ROS2는 산업용 시장을 위해 표준 방식 사용을 중요하게 여기기에 
ROS1과 비슷하게 만들기보다는 산업용 표준을 만들고 생태계를 꾸려가고 있었던 DDS를 통신 미들웨어로서 사용했음    

ROS1이 실제 산업에서 쓰기에 부적합했기 때문에,   
안정성이나 보안 등을 고려해 DDS라는 데이터 분산 시스템을 ROS2에서 쓸 수 있도록 가져왔다.    

(즉, ROS 2는 산업용 시장 진입을 위한 표준 방식 채택함)  

DDS는 Machine-to-Machine 통신 표준으로서, ROS처럼 발행-구독 시스템으로, 데이터를 읽기/쓰기할 수 있도록 도와준다.  

가장 큰 특징으로 QoS(Quality of Service)이다.  
이는 데이터 통신에서 전달신뢰성을 중요시할것인지 아니면 속도를 중요시할 것인지를 사용자가 설정할 수 있도록 함  

#### - DDS의 특징
1. Publish/Subscribe 모델 : ROS가 채택한 모델과 유사하다.    
데이터를 Pub-Sub하는 시스템을 사용함

2. 퍼포먼스 : pub/sub모델 채택으로 request/reply 모델에 비해 지연시간이 적고 단위 시간당   데이터 처리량이 높다.  
이에 real-time 퍼포먼스가 가능해진 환경을 가지게 됩니다.   
real-time 환경이 되었을 뿐 리얼타임이 자동으로 지원되는 거은 아님

3. Automatic Discovery of remote participants(Dynamic Discovery): Remote participants(따로 생성된 노드들이라 생각하면 쉬움)의 연결을 좀 더 쉬워졌다.  
Discovery Protocol을 사용하여 퍼블리셔들이 서브스크라이버들의 존재를 파악하고 각 노드들이 서로 매칭합니다.   
(이로 ROS2에서는 ROS Core가 사라졌음)

4. QoS 파라미터 : reliability, persistence, redundancy, lifespan, transport settings, resources등, 통신에 있어 여러 설정값 변경이 가능해졌다.  

#### - RTPS(Real Time Publish Subscribe)란
Real Time Publish Subscribe의 약어  

DDS가 제조사마다 다를 경우 다른 DDS 간 통신이 안될 수가 있는데,
이를 방지하기 위해 브릿지 역할격인 표준적인 wire-protocol로서 RTPS라는 것을 만들었다.   

RTPS를 통해 ROS2의 best-effort 혹은 reliable pub-sub이 가능해지고 또 QoS의 성질을  
가지게 됨  



#### - Ros2에서 DDS통신 방식
![다운로드](https://github.com/user-attachments/assets/1f300e4e-fdd7-40b2-a866-aca87317b005)  
Data Writer는 Publisher, Data Reader는 Subscriber라 생각하면 됨  

1. Publisher: **발행자** 라는 뜻으로 특정 주제(Topic)를 발행한다.   
이렇게 Publisher가 발간 한 주제는 Subscriber가 구독을 하게 된다.
1. Subscriber: **구독자**라는 뜻으로 여러 주제(Topic)들을 읽을 수 있다. 
DDS에서의 Subscriber는 발간자가 누군지는 알지 못해도, 단지 주제(Topic)만 정확히 알고 있다면, 어떤 발행자(Publisher)든지 발행을 하고 있는 중이라면 해당 데이터를 받을 수있다.  
1. Topic: **주제**라는 뜻으로 통신의 관점에서는 통신하는 내용이 담겨져 있는 Message라고 생각하면 된다.  
2. Participant: **참가**라는 의미를 지니고 있으며 특정 Domain에 참여하고 여러개의 Publisher와 Subscriber들을 가지고 있다.
3. Domain: Domain은 Participant들이 활동하는 영역이며, 같은 Domain 내에서만 정보전달을 할 수 있다.
   
DDS에서 전송시스템은 DDS 미들웨어가 알아서 해주기 때문에, 실제 시스템 개발자는 위의 Domain, Participant, Publisher, Subscriber, Topic만 구현을 하게 된다.  
즉, 실제로 개발자가 신경쓸 부분은 Topic과 Publisher, Subscriber라고 볼 수 있다.

![다운로드](https://github.com/user-attachments/assets/df76bf71-c6e9-4a9e-bbb6-3a6eec49d50b)  

ROS2에서 Node를 생성 코드를 작성하게 되면, 이를 DDS에서는 RTPS를 이용하여 Participant라는 엔티티를 만듬  

여기서 엔티티는 QoS를 설정할 수 있고, Status 값을 가지는 객체를 일컫는데, DDS에서는 DomainParticipants, Topics, Publishers, DataWriters, Subscribers, DataReaders 들을 전부 엔티티라 부른다.  

Node에 Publisher를 생성하게 되면 Participant(Node)는 Publisher 엔티티를 만들게 되고
Publisher 엔티티는 DataWriter엔티티를 생성

반대로 Node에 Subsccriber를 생성하게 되면 Participant(Node)는 Subscriber 엔티티를 만들게 되고 Subscriber 엔티티는 DataReader 엔티티를 생성함  

이렇게 Participant는 Publisher와 Subscriber를 가지고 있다.   
둘 다 가지고 있을 수도, 둘 중 하나만 가지고 있을 수도 있다.   
또한 같은 DDS Domain위의 다른 Participant와 Topic을 주고 받는다.  

ROS 2에서의 노드는 바로 이 Participant이다.   
즉, ROS 2에서의 Node는 Publisher와 Subscriber를 가지며 같은 Domain위의 다른 노드들과 Topic을 주고 받으며 통신한다.

### - QoS(통신 서비스 품질, Quality of Service)  
QoS란 Quality of Service의 약어이며 Mission Critical 서비스의 품질을 관리하기 위해 사용하는 네트워크 트래픽 제어 기술이다.  
로봇 시스템에서는 제한된 네트워크 환경에서 효율적으로 통신을 관리하고 실시간 데이터를 안정적으로 전송하는 것이 필수적이다.  

QoS가 필요한 환경은 대역폭 부족과 같은 네트워크 자원의 부족의 극복하기 위해서 사용하는 경우가 대부분 이다.  
제한된 자원 사용에 있어 중요도가 높은 트래픽은 전송하고 그렇지 못한 트래픽은 폐기를 통해   
서비스 품질을 관리하는 것이 QoS의 기술의 핵심이다.   
즉, 전체 서비스 품질이 좋아지는 것이 아닌 일부 서비스의 품질은 떨어지지만 중요한 트래픽의 서비스 품질을 보장하는 기술이다. 

ROS2에서는 TCP처럼 신뢰성을 중시 여기는 통신 방식과 UDP처럼 통신 속도에 focus를 맞춤 통신 방식을 선택적으로 사용할 수 있다.  

이를 위해 DDS의 QoS를 도입하여 Publisher/Subscriber 등을 선언할 때 QoS를 매개변수 형태로 지정할 수 있어서 원하는 데이터 통신의 옵션 설정을 유저가 직접할 수 있다. 

#### - ROS 2에서 사용되는 QoS 옵션
ROS 2에서는 DDS에서 제공하는 22개의 QoS 중에서 History, Reliability, Durability, Deadline, Lifespan, Liveliness를 지원한다.   
ROS 2에서는 이 중 대표적으로 Reliability가 사용된다.  

~~~
History :데이터를 몇 개나 보관할지 결정하는 옵션  
Reliability : 데이터 전송에서 속도를 우선하는지 신뢰성을 우선하는지를 결정하는 옵션  
Durability : 데이터를 수신하는 Subscribe가 생성되기 전의 데이터를 사용할 것인지에 대한 옵션  
Deadline : 정해진 주기 안에 데이터가 발신, 수신되지 않을 경우 이벤트 함수를 실행하는 옵션  
Lifespan : 정해진 주기 안에서 수신되는 데이터만 유효 판정하고, 그렇지 않은 데이터는 삭제하는 옵션  
Liveliness : 정해진 주기 안에서 노드 혹은 토픽의 생사를 확인하는 옵션  
~~~

#### - QoS Profile 설정 코드
ROS 2의 RMW에서 QoS 설정을 쉽게 사용할 수 있도록 많이 사용하는 QoS 설정을 세트로 표현해둔 것을 RMW QoS Profile라고 한다.  

~~~python
 QOS_RKL10V = QoSProfile(
        reliability=QoSReliabilityPolicy.RELIABLE,
        history=QoSHistoryPolicy.KEEP_LAST,
        depth=qos_depth,
        durability=QoSDurabilityPolicy.VOLATILE
    )
~~~

~~~
RELIABLE: 메시지의 손실 없이 전달되도록 보장합니다. 네트워크 상황이 좋지 않아도, 메시지가 수신될 때까지 재전송을 시도
반대 옵션은 BEST_EFFORT로, 이는 네트워크 상태에 상관없이 메시지를 즉시 전달하며, 일부 메시지가 손실될 수 있음

KEEP_LAST: 이력 관리 정책, 최근의 메시지 N개만 유지한다. 
여기서 N은 depth로 정의 반대 옵션으로 KEEP_ALL은 모든 메시지를 저장하는 방식이지만, 메모리 사용이 커질 수 있음

qos_depth: 히스토리의 깊이, depth는 이력에서 유지할 메시지의 개수를 설정합니다. 
예를 들어, depth=10으로 설정하면 최신 메시지 10개만 저장하고, 
그 이후의 메시지가 오면 가장 오래된 메시지가 제거

VOLATILE: 지속성 정책, 새로운 구독자가 구독을 시작할 때, 구독 이전에 발행된 메시지는 수신하지 않습니다. 즉, 발행된 메시지를 즉시 처리하고 보관하지 않는다. 
반대 옵션인 TRANSIENT_LOCAL은 구독자가 나중에 구독을 시작하더라도, 발행된 메시지가 메모리에 남아 있다면 그것을 받을 수 있음

참고, 
VOLATILE는 Hot Observer와 비슷하다.
구독자가 구독을 시작하기 전에 발행된 메시지는 받을 수 없음
즉, 이전 메시지가 저장되지 않고 발행 시점 이후의 메시지부터 수신

TRANSIENT_LOCAL은 Cold Observer와 유사
구독자가 구독을 시작하기 전에 발행된 메시지도 수신할 수 있습니다. 
발행된 메시지가 일정 기간 동안 발행자의 메모리에 저장되어 있다가, 나중에 구독한 구독자에게도 전달된다.
즉, 구독 시점에 상관없이 전달될 수 있음
~~~



### - ROS2 Node생성 및 통신

#### - Subscriber 노드 생성
~~~bash
ros2 run demo_nodes_cpp listener
~~~

#### - Publisher 노드 생성
~~~bash
ros2 run demo_nodes_cpp talker
~~~

#### - Subscriber, Publisher 메세지 통신 결과
![스크린샷 2024-10-03 오후 8 09 41](https://github.com/user-attachments/assets/d85d8f08-1ca1-4e68-80d5-e9f747dcfcfb)

#### - Node Graph
![스크린샷 2024-10-03 오후 8 14 51](https://github.com/user-attachments/assets/9dcc08f2-b3e7-4d24-94e4-74be62ab1f2b)

여기서 /chatter는 Topic이름   
talker 노드는 /chatter라는 토픽에 텍스트 메시지를 계속해서 보내고, listener 노드는 그 메시지를 실시간으로 받아서 출력하는 구조이다. 

### - ROS2 Turtlesim 통신
Turtlesim은 ROS의 기본 기능을 가지고 있어 ROS1부터 튜토리얼로 많이 사용되는 패키지이다.  

#### - turtlesim package list
~~~bash
ros2 pkg executables turtlesim
~~~
~~~bash
// 1. 사각형 모양으로 turtle을 움직이게 하는 node
turtlesim draw_square 

// 2. 유저가 지정한 topic으로 동일 움직임의 turtle_node를 복수 개 실행시킬 수 있는 노드
turtlesim mimic 

// 3. turtlesim_node를 움직이게 하는 속도 값을 publish하는 node
turtlesim turtle_teleop_key 

// 4. turtle_teleop_key로 부터 속도 값을 topic값으로 받아 움직이게 하는 노드
turtlesim turtlesim_node 
~~~

여기서 turtlesim통신 예제에서 사용되는 node는 **turtlesim turtlesim_node**와 **turtlesim turtle_teleop_key** 이 2개 이다.  

![스크린샷 2024-10-03 오후 9 00 33](https://github.com/user-attachments/assets/10de7818-8089-44e8-9a6e-bfa7e744d802)  

#### - turtlesim node list
~~~bash
ros2 node list                  
/teleop_turtle
/turtlesim
~~~
두 명령어 입력 후 node확인 시 teleop_turtle, turtlesim 이 두 노드가 생성된 것을 확인 할 수 있다.  

![스크린샷 2024-10-03 오후 9 05 29](https://github.com/user-attachments/assets/355703e8-61c1-48c1-b17d-6d4dd2723e6a)

위 그림에서 **/turtlesim**노드는 Subscribe **/teleop_turtle**노드가 Publisher이다.  
rqt_graph에서 동그라미는 Node, 네모는 Topic 또는 Action이고 Service는 순간적으로 사용되는 개념이라  
따로 표시되지는 않음  

두 노드끼리 **/turtle1/cmd_vel**이라는 topic을 통해서 통신을 하고 있는걸 알 수 있음  

**/turtle1/cmd_vel**은 속도 명령(velocity command)을 퍼블리시하는 토픽입니다. 이 토픽은 주로 로봇의 이동 명령을 전달하기 위해 사용되며, geometry_msgs/Twist 메시지 타입을 사용

geometry_msgs/Twist 메시지 형식은 두 가지 주요 구성 요소로 나뉜다.   
•	linear: 선속도, 즉 앞으로/뒤로 이동하는 속도  
•	angular: 각속도, 즉 좌우 회전하는 속도  


#### - turtlesim topic list
~~~bash
ros2 topic list
/parameter_events
/rosout
/turtle1/cmd_vel
/turtle1/color_sensor
/turtle1/pose
~~~

• **/parameter_events**  
: 이 토픽은 파라미터의 변화에 대한 이벤트를 퍼블리시한다.     
ROS 2에서는 노드의 파라미터를 동적으로 수정할 수 있으며, 이 토픽을 통해 파라미터 변경 사항을 다른 노드에 알릴 수 있다.  

• **/rosout**  
: 이 토픽은 모든 로깅 메시지를 전송함 노드에서 발생하는 로그 메시지(정보, 경고, 에러 등)를 수집하여     
퍼블리시한다. 이를 통해 다른 노드나 사용자들이 로그 메시지를 모니터링할 수 있수 있음  

• **/turtle1/cmd_vel**  
: 이 토픽은 거북이(turtle1)의 속도 명령을 전달한다.   
퍼블리셔가 선속도(linear velocity)와 각속도(angular velocity)를 포함한 메시지를 퍼블리시하며   
turtlesim_node는 이 토픽을 구독하여 거북이를 움직이게 됨    
메시지 타입은 geometry_msgs/Twist이다.   

• **/turtle1/color_sensor**  
이 토픽은 거북이가 현재 보고 있는 색상 정보를 제공함 거북이가 보고 있는 색상의 RGB 값을 퍼블리시한다.     
이 정보를 통해 거북이가 색상에 따라 특정 행동을 하도록 제어할 수 있음    

• **/turtle1/pose**  
: 이 토픽은 거북이(turtle1)의 현재 위치 및 방향 정보를 전달한다.     
이 토픽은 turtlesim에서 거북이의 (x, y) 좌표와 각도(theta) 정보를 포함하는 메시지를 퍼블리시함     
다른 노드들은 이 토픽을 구독하여 거북이의 위치와 방향을 추적할 수 있음   

## 2. ROS2 Topic Programming

### - Topic 이란?
Topic은 비동기식 단방향 메세지 송수신 방식으로 msg인터페이스 형태의 메시지를 publish하는   
Publisher와 메세지를 Subscibe하는 Subsciber간의 통신이다.  
이는 1:1 통신을 기본으로 하지만 하나의 Topic을 송수신하는 1:N도 가능하고 그 구성 방식에 따라  
N:1, N:N 통신도 가능하다.  
ROS 메세지 통신에서 가장 널리 사용되는 방법이다.  

#### - 1:1 통신
![img](https://github.com/user-attachments/assets/c10fd52b-75d5-4456-9629-eb1d7cd61e36)

#### - 1:N 통신
![img](https://github.com/user-attachments/assets/5dd926c8-6031-4b62-95a3-f5df640b4f7f)

하나 이상의 topic을 publish할 뿐 하니라 동시에 subscribe할 수 도 있음
원한다면 셀프 subscribe할 수 도 있다.  
Topic은 메시지 통신 방식중에 가장 기본이 되는 방법이고 기본 특징으로 비동기성과 연속성을 가지기에  
센서 값 전송 및 항시 정보를 주고 받아야하는 부분에서 주로 사용됨  

즉, 비동기 통신이기 때문에 publisher는 subscriber가 응답을 받는지는 기다리지 않고  
바로바로 publish한다.  

service의 경우 service server와 client사이에서 서로 요청을 하고 응답을 받았는지  
확인 하면서 통신하는 동기 방식과 대비되는 통신 방식임  

### - bag(ros2 bag record) 기록
ros2에서 퍼블리시되는 topic을 파일 형태로 저장하고 필요할 때 저장된 topic을 다시 불러와  
동일한 주기로 재생할 수 있는 기능  
즉, 이전에 실행했던 topic기록을 다시 재생시켜주는 기능이다.  

SLAM 개발 시 센서 정보와 로봇의 위치 정보인 **오도메트리** 정보가 필요한데  
매번 로봇을 구동시켜 정보를 취득하더라도 로봇의 상태값에 따라 결괏값이 달라져서  
제대로 알고리즘을 개발한거인지 구분하기 어렵다.  

이때 이렇게 bag으로 기록하여 입력값을 고정시켜 반복 테스트할때 사용됨  

#### 저장하고자 하는 topic을 설정
~~~bash
ros2 bag record /turtle1/cmd_vel
~~~

![스크린샷 2024-10-09 20-57-09](https://github.com/user-attachments/assets/d1d36051-5a13-4e3f-8317-a316f3747191)

#### 저장한 bag파일 정보 확인
~~~bash
ros2 bag info rosbag2_2024_10_09-20_53_19
~~~

~~~bash
Files:             rosbag2_2024_10_09-20_53_19_0.db3
Bag size:          25.0 KiB
Storage id:        sqlite3
Duration:          7.498390613s
Start:             Oct  9 2024 20:53:22.852041564 (1728474802.852041564)
End:               Oct  9 2024 20:53:30.350432177 (1728474810.350432177)
Messages:          22
Topic information: Topic: /turtle1/cmd_vel | Type: geometry_msgs/msg/Twist | Count: 22 | Serialization Format: cdr

~~~

#### 저장한 bag파일을 불러와 실행
~~~bash
ros2 bag play rosbag2_2024_10_09-20_53_19
~~~

영상이 너무 커서 발표때 실행해보는걸로...


### - Topic Programming  

#### 계산기 프로그래밍 강의 2:30분쯤 argument 참고  

#### 1. Publisher (arithmetic_argument_publisher 선언부)
[Argument 전체 코드]("https://github.com/robotpilot/ros2-seminar-examples/blob/main/topic_service_action_rclpy_example/topic_service_action_rclpy_example/arithmetic/argument.py")
~~~Python
class Argument(Node):

    def __init__(self):
        super().__init__('argument') # 노드 이름 'argument'로 초기화
        self.declare_parameter('qos_depth', 10) # QoS 설정을 위한 기본 파라미터 선언
        qos_depth = self.get_parameter('qos_depth').value # 파라미터 값 가져오기

        # 최소 및 최대 랜덤 숫자에 대한 파라미터 선언
        self.declare_parameter('min_random_num', 0)
        self.min_random_num = self.get_parameter('min_random_num').value
        self.declare_parameter('max_random_num', 9)
        self.max_random_num = self.get_parameter('max_random_num').value

        # callback메서드로 여기서 1초다마 퍼블리셔 되는 값을 바꿔줌
        self.add_on_set_parameters_callback(self.update_parameter)

        ...

        # Node Class의 create_publisher를 사용하여 Publisher생성
        self.arithmetic_argument_publisher = self.create_publisher(
            '''
            ArithmeticArgument는 ROS 2의 사용자 정의 메시지 타입으로, 
            msg_srv_action_interface_example 패키지에 정의되어 있다.
            이 메시지는 산술 연산을 위해 필요한 두 개의 숫자 인수를 포함

            1. 패키지:
            • msg_srv_action_interface_example: 이 패키지는 ROS 2의 예제 및 실습을 위한 패키지로,
                메시지, 서비스, 액션 인터페이스의 사용 방법을 보여주기 위해 설계됨

            2. 메시지 정의:
            • ArithmeticArgument 메시지는 산술 연산에 필요한 두 개의 부동 소수점 숫자를 포함하여,
                연산에 필요한 값을 제공한다.

            3. 메시지 구조:
            • ArithmeticArgument 메시지는 일반적으로 다음과 같은 구조를 가진다:
                float64 argument_a  # 첫 번째 숫자
                float64 argument_b  # 두 번째 숫자

            4. 사용 예시:
            • 이 메시지는 ArithmeticArgument를 퍼블리시하는 노드에서 생성되어,
                다른 노드에서 구독할 수 있음 
                예를 들어, 두 개의 숫자를 합치는 노드를 작성하여 이 메시지를 받아 처리할 수 있다.
            • 이 메시지를 사용하는 시스템에서는 두 개의 인수(argument_a와 argument_b)를 통해
                다양한 산술 연산을 수행할 수 있음
            '''
            ArithmeticArgument, # msg_srv_action_interface_example.msg
            'arithmetic_argument', # 퍼블리시할 토픽 이름
            QOS_RKL10V # 설정한 QoS 프로파일
            ) 

        # Timer를 사용하여 1초마다 publish_random_arithmetic_arguments메서드 호출
        self.timer = self.create_timer(1.0, self.publish_random_arithmetic_arguments)

    ...
~~~

#### - publish_random_arithmetic_arguments
~~~Python
class Argument(Node):
    ...

    # 실제로 Publish가 이루어지는 부분
    def publish_random_arithmetic_arguments(self):
        msg = ArithmeticArgument() # ArithmeticArgument 타입의 메시지 생성
        msg.stamp = self.get_clock().now().to_msg() # 현재 시간을 메시지에 추가 (Topic 생성 시간)

        # min_random_num과 max_random_num 사이의 랜덤 숫자 생성 
        msg.argument_a = float(random.randint(self.min_random_num, self.max_random_num))
        msg.argument_b = float(random.randint(self.min_random_num, self.max_random_num))

        # 생성된 메시지를 퍼블리시 (Topic Publish)
        # Publish한 시간 및 변수 a, b를 저장한 메시지를 Publish한다는 뜻
        self.arithmetic_argument_publisher.publish(msg)

        # 퍼블리시된 값을 로그에 출력
        self.get_logger().info('Published argument a: {0}'.format(msg.argument_a))
        self.get_logger().info('Published argument b: {0}'.format(msg.argument_b))

    ...
~~~

~~~Python
class Argument(Node):
    ...
    # 1초마다 퍼블리셔 되는 a, b값을 바꿔주기 위한 callback메서드
    def update_parameter(self, params):
        for param in params:
            if param.name == 'min_random_num' and param.type_ == Parameter.Type.INTEGER:
                self.min_random_num = param.value
            elif param.name == 'max_random_num' and param.type_ == Parameter.Type.INTEGER:
                self.max_random_num = param.value
        return SetParametersResult(successful=True)
...
~~~

#### 2. Subscriber (arithmetic_argument_subscriber 선언부)
[Calculator 전체 코드]("https://github.com/robotpilot/ros2-seminar-examples/blob/main/topic_service_action_rclpy_example/topic_service_action_rclpy_example/calculator/calculator.py")
~~~Python
...

class Calculator(Node):
    def __init__(self):
        super().__init__('calculator')
        self.argument_a = 0.0
        self.argument_b = 0.0
        self.argument_operator = 0
        self.argument_result = 0.0
        self.argument_formula = ''
        self.operator = ['+', '-', '*', '/']
        self.callback_group = ReentrantCallbackGroup()

        self.declare_parameter('qos_depth', 10)
        qos_depth = self.get_parameter('qos_depth').value

        QOS_RKL10V = QoSProfile(
            reliability=QoSReliabilityPolicy.RELIABLE,
            history=QoSHistoryPolicy.KEEP_LAST,
            depth=qos_depth,
            durability=QoSDurabilityPolicy.VOLATILE)
       
        self.arithmetic_argument_subscriber = self.create_subscription(
            ArithmeticArgument,  # Topic Type은 Publisher부분과 동일하게 ArithmeticArgument로 선언
            'arithmetic_argument', # Topic 이름
            self.get_arithmetic_argument, # 이 콜백 메서드로 메세지를 받을 때 마다 실행
            QOS_RKL10V,
            '''
            위에 보면 ReentrantCallbackGroup을 사용하는데 
            이는 이 콜백함수를 병렬로 실행시키기 위함이다. 이렇게 설정해 주지 않으면
            MutuallyExclusiveCallbackGroup이 기본값으로 설정되는데 이 값은 한번에 하나의 콜백 함수만
            실행하도록 하는것임

            따라서 본 예제에서는 create_subscription, create_service, ActionServer 등에서 병렬로 처리하기 위해
            ReentrantCallbackGroup을 사용하였다.
            '''
            callback_group=self.callback_group)

        self.arithmetic_service_server = self.create_service(
            ArithmeticOperator,
            'arithmetic_operator',
            self.get_arithmetic_operator,
            callback_group=self.callback_group) # 여기

        self.arithmetic_action_server = ActionServer(
            self,
            ArithmeticChecker,
            'arithmetic_checker',
            self.execute_checker,
            callback_group=self.callback_group) # 여기 
    ...
~~~

#### - get_arithmetic_argument 콜백 메서드
~~~python
class Calculator(Node):
    ...

    '''
    위 subscriber 부분에서 설정한대로 arithmetic_argument 토픽으로 부터 ArithmeticArgument 타입의
    메세지를 받으면 실행됨

    msg Arguement로 메세지 내용을 받아와 화면에 표시함
    '''
    def get_arithmetic_argument(self, msg):
        self.argument_a = msg.argument_a
        self.argument_b = msg.argument_b
        self.get_logger().info('Timestamp of the message: {0}'.format(msg.stamp))
        self.get_logger().info('Subscribed argument a: {0}'.format(self.argument_a))
        self.get_logger().info('Subscribed argument b: {0}'.format(self.argument_b))

    ...
~~~

#### 3. entry_points 노드작성 (setup.py)
[setup.py 전체 코드]("https://github.com/robotpilot/ros2-seminar-examples/blob/main/topic_service_action_rclpy_example/setup.py")
~~~python
#!/usr/bin/env python3

import glob
import os

from setuptools import find_packages
from setuptools import setup

package_name = 'topic_service_action_rclpy_example'
share_dir = 'share/' + package_name

'''
    entry_points에 실행할 스크립트의 이름과 호출 함수를 작성함
    본 예제에서는 4개의 Node를 작성하고 추후 (ros2 run ...) 명령어로 실행

    argument는 argument.py에 main함수가 있고
    calculator는 main.py에 main함수가 있다. (경로가 좀 다름)
    그래서 ... argument:main, ... calculator.main:main 으로 작성 됨
'''

setup(
    ...
    entry_points={
        'console_scripts': [
            'argument = topic_service_action_rclpy_example.arithmetic.argument:main',
            'operator = topic_service_action_rclpy_example.arithmetic.operator:main',
            'calculator = topic_service_action_rclpy_example.calculator.main:main',
            'checker = topic_service_action_rclpy_example.checker.main:main',
        ],
    },
)
~~~

#### - calculator/main.py (calculator 노드 생성부)
~~~python
import rclpy
from rclpy.executors import MultiThreadedExecutor

from topic_service_action_rclpy_example.calculator.calculator import Calculator


def main(args=None):
    rclpy.init(args=args)
    try:
        calculator = Calculator()
        '''
        MultiThreadedExecutor를 사용하야 thread를 4개 사용하는 executor를 생성
        (executor: 노드에서 발생하는 통신 이벤트를 적절히 처리하기 위해 사용되며, 실행 흐름을 관리)
        참고: threads를 지정하지 않으면 cpu에서 사용가능한 스레드 수 자동 할당
        이것도 안되면 단일 스레드로 작동된다.

        MultiThreadedExecutor는 스레드 풀을 사용하여 콜백을 실행하게 해줌
        이렇게 생성한 executor는 콜백이 병렬로 발생하도록 허용하여
        ReentrantCallbackGroup와 함께 사용하면 콜백 함수를 병렬로 실행할 수 있게 됨
        즉, 병렬 처리 부분
        '''
        executor = MultiThreadedExecutor(num_threads=4) 
        executor.add_node(calculator)
        try:
            executor.spin()
        except KeyboardInterrupt:
            calculator.get_logger().info('Keyboard Interrupt (SIGINT)')
        finally:
            executor.shutdown()
            calculator.arithmetic_action_server.destroy()
            calculator.destroy_node()
    finally:
        rclpy.shutdown()


if __name__ == '__main__':
    main()
~~~

#### 4. Calculator, Argument 실행
#### - Calculator(Subscriber) 실행
~~~bash
ros2 run topic_service_action_rclpy_example calculator
~~~
#### - Argument(Publisher) 실행
~~~bash
ros2 run topic_service_action_rclpy_example argument
~~~

#### - Calculator, Argument 실행 영상
![스크린캐스트-2024년-10월-07일-21시-40분-47초](https://github.com/user-attachments/assets/f700c8e1-ceee-4faa-bc25-b2a7a2dec4c3)

#### - rqt Node Graph
![스크린샷 2024-10-09 18-07-51](https://github.com/user-attachments/assets/f9d5ca02-cad8-4804-8fb6-0d16167fe2b0)
