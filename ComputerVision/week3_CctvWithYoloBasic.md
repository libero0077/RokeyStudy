# 3차시: 딥러닝 컴퓨터비전 p288-307

### 기본 개념

1. YOLOv5s
    - 단일 신경망이라, 이미지 전체를 한번에 분석함
    - 모델 크기가 s, m, l, x로 다양하게 있음
        
        s가 성능(정확도)이 제일 낮지만 FPS가 가장 높고, x가 성능이 제일 높지만 FPS는 가장 낮음 
        
        *FPS = 초당 frame 수 (객체 감지 비율) ; FPS가 클수록 frame이 많아 자연스러움
        
        ex) 30fps = 초당 30개의 frame에 대해 detection 수행 
        
        - s: 속도가 빨라 실시간 처리에 적합 ; 작은 객체 탐지는 놓칠 수 있음
        - x: 속도가 매우 느릴 수 있지만 정확도가 중요한 작업에 적합 ; 작은 객체도 탐지 잘함
    - 구조
        
        ![image](https://github.com/user-attachments/assets/74f6bdbe-135f-4952-82ba-98725707211d)
        
        - Bakcbone(백본): 입력 이미지를 받아 특징을 뽑아내 feature map으로 변형시켜줌
        - Neck(넥): Backbone과 Head를 연결하는 부분으로, feature map 정제하고 재구성 → 모델이 다양한 크기와 스케일의 객체에 일반화되도록
        - Head(헤드): 물체의 bounding box로 좌표 등 위치정보와 너비 및 높이 정보, 클래스 확률(predict class) 출력
            
            추출된 feature map을 바탕으로 물체의 위치 찾음 
            
            anchor box(default box)를 처음에 설정하고 이를 이용해서 최종 bounding box 생성  
            
            - Dense prediction: One-stage detector: predict classes와 bounding box regression 통합 ; 한번에 bounding box와 물체 종류 같이 예측-속도 빠름 ex) YOLO, SSD
            - Sparse prediction: Two-stage detector: predict classes와 bounding box regression 분리 ; 따로 예측-속도 느리지만, 더 정확 ex) R-CNN, R-FCN
    - 이전까지 데이터 설정은 obj.names, obj.data를 사용했지만, v5는 YAML 형식의 데이터셋 설정 파일 사용
        - obj.names
            
            ```python
            # 클래스의 개수가 2개이고 각각, dog 와 cat인 경우
            dog
            cat
            ```
            
        - obj.data
            
            ```python
            classes = 2 # 클래스 개수
            # 파일 경로  
            train = data/train.txt 
            valid = data/test.txt
            names = data/obj.names
            # weight 파일 백업할 경로 
            backup = backup/
            ```
            
    - 데이터셋
        - VOC 데이터셋: 인덱스 0부터 19까지 20개 클래스 (aeroplane, bicycle, bird 등)
        - COCO 데이터셋: 80개 클래스 지원
2. YOLOv7-tiny
    - YOLO-tiny: 각 YOLO 버전의 경량화 버전 (FPS가 높고 자원 소모가 적음)
        - 경량화
        - 빠른 처리 속도
        - 적은 계산량
    - YOLOv4와 저자 동일 → 차이점
        1. 보조 분류용 헤드: 메인 헤드 외에 추가적인 헤드 도입
            
            → 객체를 더 정확하게분류하기 위해
            
            - 다양한 크기와 비율의 객체 인식할 때 성능 향상 (작은 객체나 복잡한 배경에서도 탐지 가능)
        2. 매개변수 재정의 콘볼루션: 기존의 합성곱 방식에서 매개변수를 재정의해서 훈련 시 다양한 최적화 연산을 해 훈련이 잘되는 구조 적용
        3. E-ELAN 구조: 여러 층의 네트워크 통합-계층 집계 네트워크
            - 그룹 콘볼루션 사용
            - 서로 다른 그룹 feature들을 셔플 및 병합
            
            ![image (1)](https://github.com/user-attachments/assets/d72615a9-b432-4266-998d-2f0b1d3c1493)

---

### 프로젝트

> YOLO를 활용한 횡단보도 보행자 보호 시스템
> 
1. 시나리오
    - **교차로에서 우회전**하는 차량 운전자의 부주의로 **횡단보도 건너는 사람**과 사고의 위험성
    - 차량 우회전 시, 횡단보도 미리 확인하도록 하는 방법
        - 횡단보도 촬영하는 CCTV가 운전자가 확인할 수 있는 위치에 설치된 화면을 통해 보여줌
        - 동시에 해당 횡단보도에 보행자가 검출된 경우, 사람이 있다고 화면에 보여줌
    
    즉, 횡단보도를 촬영하면서 보행자 감지 + 감지 시, 화면에 보행자가 있으니 우회전을 금지한다는 내용 표시하는 프로젝트 
    
2. 알고리즘
    
    : 보행자 인식을 위한 객체 인식 알고리즘
    
    - 참고
        - yolo v4(darknet) → v7
        - yolo v5(pytorch) → v8
        
        | YOLO 버전 | 언어/프레임워크 | 깃허브 |
        | --- | --- | --- |
        | v4 | C++, DarkNet | ‣ |
        | v5 | Python, PyTorch | ‣ |
        | v6 | Python, PyTorch | ‣ |
        | v7 | Python, PyTorch | ‣ |
        | v8 | Python, PyTorch | ‣ |
3. 추론 개요
    - 카메라 프레임 읽기
    - YOLO 모델로 영상분석
    - 사람감지
        - 감지하면 → 감지결과 바운딩 박스 표시 & 카메라 영상 디스플레이
        - 감지 못하면 → 카메라 영상 디스플레이

---

### YOLOv5s

- git 주소: https://github.com/jetsonai/DeepLearning4Projects/tree/main/Chap7
1. Pascal VOC 데이터셋 다운 & 압축 풀기 
    
    ![image (23)](https://github.com/user-attachments/assets/0d7c9f63-2643-4e32-be35-13bdf7cb1336)
    
    이때, xml 포맷(라벨링 파일)을 txt로 변환 필요 (convert2Yolo 깃허브 저장소에서 yolo 훈련에 사용할 수 있도록 변환하는 기능 제공) ; 데이터셋들을 yolo 저자가 만든 darknet 프레임워크가 사용하는 label format으로 변경해줘야 함 
    
    > 라벨링 파일
    > 
    > 
    > ![image (2)](https://github.com/user-attachments/assets/0face865-66ec-4afc-ba58-0698c4f977db)
    > 
    > ```python
    > 14 0.538 0.452 0.36 0.5
    > # 14: 클래스 번호 ; 인덱스 14 = person 
    > # 중심 x좌표 (0~1 사이 소수점으로 나타냄): (xmin+xmax)/2
    > # 중심 y좌표: (ymin+ymax)/2
    > # 박스 너비 (ex: 0.36이면 이미지 너비의 36%가 바운딩 박스라는 뜻): xmax-xmin
    > # 박스 높이: ymax-ymin
    > ```
    > 
2. 변환 전에, Pascal VOC 데이터셋의 클래스 리스트가 있는 파일 생성
    
    ```python
    # PASCAL VOC 데이터셋 클래스 이름을 리스트로 정의
    classes = ["aeroplane\n", "bicycle\n", "bird\n", "boat\n", "bottle\n",
               "bus\n", "car\n", "cat\n", "chair\n", "cow\n", "diningtable\n",
               "dog\n", "horse\n", "motorbike\n", "person\n", "pottedplant\n",
               "sheep\n", "sofa\n", "train\n", "tvmonitor"]
    
    # 'vocnames.txt'라는 파일을 쓰기 모드('w')로 열기
    # 이 파일이 없으면 생성되고, 있으면 기존 내용을 덮어씁니다.
    with open("vocnames.txt", 'w') as f:
        # 클래스 이름 리스트를 텍스트 파일에 한 줄씩 쓰기
        f.writelines(classes)
    ```
    
    파일명: vocnames.txt 
    
    ![image (5)](https://github.com/user-attachments/assets/58672ded-35e1-4cf7-96d4-dbebff0e20fa)
    
    ![image (3)](https://github.com/user-attachments/assets/cdf41254-69a2-4df4-858a-2da441ec893a)
    
    ![image (4)](https://github.com/user-attachments/assets/258bdd4a-3465-4706-b5d7-e90e196fa0dc)
    
3. darknet 프레임워크에서 요구하는 레이블 포맷으로 변환 
    
    convert2Yolo 저장소의 [example.py](http://example.py) 활용해 xml 파일(라벨링 파일 ; Annotations 폴더 → ex-2007_000027.xml)을 txt 파일로 변환 
    
    - 파라미터 종류
        
        https://deepbaksuvision.github.io/Modu_ObjectDetection/posts/02_02_Convert2Yolo.html
        
        ![image (6)](https://github.com/user-attachments/assets/d3578528-25d4-4be7-a9ae-394e527f3320)
        
        1. datasets: 어떤 데이터셋으로 진행할건지 
            
            → COCO, VOC, UDACITY, KITTI 지원하는 데이터셋 4가지 중 하나 입력 
            
            ```python
            --datasets VOC
            ```
            
        2. img_path: 데이터셋 이미지 폴더 경로 
            
            ```python
            --img_path ../VOCdevkit/VOC2012/JPEGImages/
            ```
            
        3. label: 데이터셋 레이블(xml) 폴더 경로 
            
            ```python
            --label ../VOCdevkit/VOC2012/Annotations/
            ```
            
            ![image (7)](https://github.com/user-attachments/assets/57413c29-ffb5-4953-a089-aa79ce7aa1cf)
            
        4. convert_output_path: 변환된 레이블 파일이 저장될 폴더 경로 
            
            ```python
            --convert_output_path ../VOCdevkit/VOC2012/JPEGImages/ 
            ```
            
            ![image (8)](https://github.com/user-attachments/assets/6cda4dfb-8b52-4573-bb2b-df4ff5321110)
            
            원래 JPEGImages 폴더에 jpg 파일들만 있었는데, 코드 실행하면 xml 형식의 레이블이 txt로 변환되어 폴더에 들어갈 것
            
        5. img_type: img 폴더 확장자 명시 
            
            ```python
            --img_type ".jpg"
            ```
            
        6. manipast_path: darknet 프레임워크로 학습할 경우엔 데이터셋 이미지가 어디있는지 이미지마다 파일 경로가 적혀있는 txt 파일 요구 
            
            ```python
            --manifest_path ../ 
            ```
            
        7. cls_list_file: darknet 프레임워크에서 클래스 목록을 참조하여 학습하고 추론할 수 있도록
            
            ```python
            --cls_list_file ../vocnames.txt
            ```
            
    
    ---
    
    훈련 이미지 있는 폴더 경로: /content/VOCdevkit/VOC2012/JPEGImages/
    
    **txt 텍스트 파일 경로 = 이미지 파일 경로 
    
    왜? YOLOv5 훈련 시, 이미지와 같은 경로에 이미지와 동일한 이름의 라벨링된 텍스트 파일이 있어야 훈련 수행 가능!
    
    - txt 파일 내용: “클래스 x y h w” (금요일에 라벨링해서 얻었던 것처럼)
        
        ex-2007_000027.txt
        
        ```python
        14 0.538 0.452 0.36 0.5
        ```
        
    
    ![image (9)](https://github.com/user-attachments/assets/6c873adf-57d5-41b7-811c-3b026c48338a)
    
4. 데이터 분할+이동: 훈련 데이터와 검증 데이터로
    - YOLOv5는 데이터가 있는 폴더의 경로(JPEGImages 폴더에 섞여 있음)만 있으면 훈련 가능
    - 이미지와 라벨 파일을 7:3으로 나누고, 훈련용 데이터 폴더와 검증용 데이터 폴더에 분할해 복사
    - 디렉토리 생성 “VOCData 폴더 생김”
        - val_root: /content/VOCData/val
        - train_root: /content/VOCData/train
        
        ```python
        # 디렉토리 생성
        # 데이터셋이 있는 폴더 경로
        data_root = "/content/VOCData"
        # val, train 데이터 디렉토리 정의
        val_root = os.path.join(data_root, "val") # /content/VOCData/val
        train_root = os.path.join(data_root, "train") # /content/VOCData/train
        # val, train 디렉토리 없으면 생성
        os.makedirs(val_root, exist_ok=True)
        os.makedirs(train_root, exist_ok=True)
        ```
        
        ![image (10)](https://github.com/user-attachments/assets/91722bbc-f1e2-4ec3-b113-2d783c299998)
        
    - manifest.txt: 이미지 파일 경로 목록
        
        ![image (11)](https://github.com/user-attachments/assets/99e29475-42dc-4494-b6e4-1c44f34ff37e)
        
    - 기존 경로 처리 (4가지로)
        - img_src: /content/VOCdevkit/VOC2012/JPEGImages/2011_006380.jpg
        - txt_src: /content/VOCdevkit/VOC2012/JPEGImages/2011_006380.txt
        - img_name: 2011_006380.jpg
        - text_name: 2011_006380.txt
    - 새로 분할할 위치의 경로 설정
        - 앞에서 설정한 img_name 이미지 파일 이름과 text_name 텍스트 파일 이름을 30%까진 검증 데이터 디렉토리와 합쳐서 경로 설정
        - 이후 나머지 70%는 훈련 데이터 디렉토리와 합쳐서 경로 설정
    - JPEGImages 폴더 안에 있는 이미지와 텍스트 파일을 각각 설정한 경로 train, val로 복사
        
        
        ![image (12)](https://github.com/user-attachments/assets/a52cb80e-95df-4851-ab30-f5c7e0e6abc7)
        
        ![image (13)](https://github.com/user-attachments/assets/fc388dbd-f9e3-4b02-96db-9c6fe4ef910e)
        
    
    > 데이터셋 준비 완료
    > 
5. YOLOv5 환경 구성
    - YOLOv5 GitHub 저장소에서 복사
    - 필요한 패키지 설치
        
        ```python
        %pip install -qr requirements.txt
        ```
        
        ![image (14)](https://github.com/user-attachments/assets/9bd36ac8-845f-4cba-8260-407c517ec8b8)
        
    - 환경 테스트: yolov5의 detect.py로 확인
        
        → 코드 실행하면 runs 폴더 생성됨 
        
        - 코드
            
            ```python
            !python detect.py --weights yolov5s.pt --img 640 --conf 0.25 --source data/images
            ```
            
            → [detect.py](http://detect.py) ; 다음과 같은 파라미터 지정해주면 코드 수행 
            
            1. weights: 가중치 파일 지정 ; [yolov5s.pt](http://yolov5s.pt) 모델의 가중치 사용 
            2. img: 입력 이미지 크기 설정 ; 640x640으로 조정한 후 탐지 수행
            3. conf: 탐지 신뢰도 기준 설정 ; 0.25 이상의 신뢰도 가진 탐지 결과만 출력 
                
                class score가 설정한 값을 넘겨야 바운딩 박스를 그림 
                
            4. source: 탐지할 이미지가 있는 경로 설정 “data/images”
                
                ![image (15)](https://github.com/user-attachments/assets/23bbc195-5305-4214-aef8-abbec31e6f68)
                
                ![image (16)](https://github.com/user-attachments/assets/8e414e94-7c36-4ccf-be6b-5b8c2770354f)
                
            5. —view-img: 이후 영상 추론에서는 해당 옵션이 활성화 되는데, 이건 모델이 감지한 결과를 실시간으로 화면에 표시해주는 기능
        - 테스트 파일 저장 경로
            
            ![image (17)](https://github.com/user-attachments/assets/df1a37f2-1bee-490a-8335-16352ca03826)
            
        - 객체 탐지 정상 수행
            
            ![image (18)](https://github.com/user-attachments/assets/df493c2e-031d-40e5-8c31-a9e57176c578)
            
6. yaml 파일 제작 
    
    ```python
    with open("/content/yolov5/vocdata.yaml", 'w') as f:
        f.write(text_lines)
    ```
    
    - yaml 파일: YOLOv5 학습을 위한 데이터 설정 파일
    - 학습 시, yaml 파일을 통해 모델이 학습할 데이터 경로와 클래스 파악
    - 클래스 이름 한 줄씩 나열
    
    ![image (19)](https://github.com/user-attachments/assets/448da813-7833-4d70-b193-787138ae16d2)
    
    [구성 정보]
    
    - 훈련 데이터셋 경로
    - 검증 데이터셋 경로
    - 각 클래스 인덱스당 클래스 이름 (객체 탐지에 사용)
7. YOLOv5 훈련 
    
    ```python
    !python train.py --img 480 --batch 16 --epochs 20 --data vocdata.yaml --weights yolov5s.pt --cache
    ```
    
    - —img 480: 모든 입력 이미지 크기를 480x480으로
    - —batch 16: 배치크기 16 ; 한 번의 훈련 단계에서 16개 이미지 처리
    - —epochs 20: 훈련 20에폭 동안 수행 ; 에폭 = 전체 데이터셋 한 번 훈련하는 과정
    - —data: 훈련에 사용할 데이터셋 설정 ; vocdata.yaml 데이터 경로, 클래스 수 등의 정보 포함
    - —weights: 훈련 시작할 때 불러올 pre-trained 모델 (yolov5s.pt ; COCO 데이터셋 활용해 훈련한 파일) → 사전 훈련된 가중치
        
        weights(모델 파라미터)나 cfg(네트워크 ; 레이어, 활성화 함수 등) 둘 중 하나 반드시 입력해야 함 
        
    - —cache: 훈련 시 데이터를 빠르게 로드하기 위해
8. weight 파일 다운로드 
    
    ![image (20)](https://github.com/user-attachments/assets/b2d5cd47-16ae-4016-a96b-08a596d75e8d)
    
    - best.pt: 가장 성능 좋은 weight 파일
    - last.pt: 최신 weight 파일
9. 윈도우에서 YOLOv5 추론 실습 
    - 실습 파일들 다운
        
        ```python
        ynu@ynu-ROKEY:~$ git clone https://github.com/jetsonai/DeepLearning4Projects
        'DeepLearning4Projects'에 복제합니다...
        remote: Enumerating objects: 1268, done.
        remote: Counting objects: 100% (279/279), done.
        remote: Compressing objects: 100% (145/145), done.
        remote: Total 1268 (delta 220), reused 130 (delta 130), pack-reused 989 (from 1)
        오브젝트를 받는 중: 100% (1268/1268), 163.71 MiB | 4.71 MiB/s, 완료.
        델타를 알아내는 중: 100% (727/727), 완료.
        ```
        
    - Chap7 폴더 들어가기
        
        ```python
        ynu@ynu-ROKEY:~$ cd DeepLearning4Projects/Chap7
        ```
        
    - 실습에 필요한 yolov5 저장소 복제
        
        ```python
        ynu@ynu-ROKEY:~/DeepLearning4Projects/Chap7$ git clone -b v7.0 https://github.com/jetsonai/yolov5
        'yolov5'에 복제합니다...
        pyremote: Enumerating objects: 15656, done.
        remote: Total 15656 (delta 0), reused 0 (delta 0), pack-reused 15656 (from 1)
        오브젝트를 받는 중: 100% (15656/15656), 14.58 MiB | 3.59 MiB/s, 완료.
        델타를 알아내는 중: 100% (10688/10688), 완료.
        ```
        
    - 데이터 분석, 시각화, 시스템 모니터링 기능의 라이브러리 설치
        
        ```python
        ynu@ynu-ROKEY:~/DeepLearning4Projects/Chap7$ python3 -m pip install tqdm thop seaborn ipython psutil pyyaml
        Defaulting to user installation because normal site-packages is not writeable
        Collecting tqdm
          Downloading tqdm-4.66.5-py3-none-any.whl (78 kB)
             ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 78.4/78.4 KB 2.1 MB/s eta 0:00:00
        Collecting thop
          Downloading thop-0.1.1.post2209072238-py3-none-any.whl (15 kB)
        Collecting seaborn
          Downloading seaborn-0.13.2-py3-none-any.whl (294 kB)
             ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 294.9/294.9 KB 3.1 MB/s eta 0:00:00
        Requirement already satisfied: ipython in /home/ynu/.local/lib/python3.10/site-packages (8.27.0)
        Requirement already satisfied: psutil in /usr/lib/python3/dist-packages (5.9.0)
        Requirement already satisfied: pyyaml in /usr/lib/python3/dist-packages (5.4.1)
        Requirement already satisfied: torch in /home/ynu/.local/lib/python3.10/site-packages (from thop) (2.4.1)
        Collecting pandas>=1.2
          Downloading pandas-2.2.2-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (13.0 MB)
             ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 13.0/13.0 MB 3.2 MB/s eta 0:00:00
        ```
        
    - 훈련을 끝까지 시켜보지 않아서, 저장소의 파일 복사해서 [best.p](http://best.py)t 사용
        
        → 이때 윈도우 아니고 우분투에서 시도하는거면 copy 말고 cp 명령어 써야 함! 
        
        ```python
        ynu@ynu-ROKEY:~/DeepLearning4Projects/Chap7$ cp yolov5s_voc.pt yolov5/
        ```
        
        ![image (21)](https://github.com/user-attachments/assets/239bba44-24c8-4fdf-aac2-5ee59406e3f0)
        
    - 추론 수행
        
        ![image (22)](https://github.com/user-attachments/assets/25673c8e-2388-414b-b896-94b9ae44d4b4)
        
        ```python
        cd DeepLearning4Projects/Chap7/yolov5
        python3 detect.py --weights yolov5s_voc.pt --img 640 --conf 0.25 --source ../data/crosswalk_cctv_01.mp4 --view-img
        ```
        
    - 결과 일부 캡처
        
        ![crosswalk_cctv_01 mp4_screenshot_18 09 2024](https://github.com/user-attachments/assets/fcd6a5b7-9535-4c6a-bfa3-3f73e545d62e)
        
        ![crosswalk_cctv_01 mp4_screenshot_18 09 2024 (1)](https://github.com/user-attachments/assets/33cd960f-62c9-48e1-b7c0-0e0609936f9a)
        
        ![crosswalk_cctv_01 mp4_screenshot_18 09 2024(1)](https://github.com/user-attachments/assets/f08c8e4d-9bcf-4ad3-8fef-196055773c1c)
        
    - 이때 —source 이후에 0을 입력하면 웹캠으로부터 영상 받아서 추론
        
        ```python
        python3 detect.py --weights yolov5s_voc.pt --img 640 --conf 0.25 --source 0 --view-img
        ```
        
        ```python
        0: 480x640 1 person, 160.2ms
        0: 480x640 1 person, 152.1ms
        0: 480x640 1 person, 148.1ms
        0: 480x640 1 person, 97.4ms
        ```
        

---

### YOLOv7-tiny

> 이전과 과정이 모두 동일한데, yolov5 사용했던 부분을 yolov7 사용한 것만 다름
> 
1. 데이터셋 준비
2. YOLOv7 환경 구성
    
    ```python
    !git clone https://github.com/WongKinYiu/yolov7 # YOLOv7 모델과 관련된 코드, 모델 가중치, 데이터셋 등 복사
    # yolov7 디렉토리로 이동
    %cd yolov7
    %pip install -qr requirements.txt  # 필수 패키지 설치
    ```
    
    - 깃 저장소
    - 패키지 설치
    - 이때, numpy 버전 호환 문제가 발생했음 → 버전 업그레이드 실행
        
        ```python
        %pip uninstall numpy
        %pip install numpy==1.24.4
        ```
        
3. 훈련 전 테스트
    
    ```python
    !python detect.py --weights yolov7-tiny.pt --img 640 --conf 0.25 --source inference/images
    ```
    
    - detect.py
        
        ```python
        import argparse  # argparse 모듈 임포트: 명령행 인자를 파싱하는 데 사용
        import time  # time 모듈 임포트: 시간 관련 함수 사용
        from pathlib import Path  # Path 모듈 임포트: 파일 경로 조작
        
        import cv2  # OpenCV 모듈 임포트: 이미지 및 비디오 처리
        import torch  # PyTorch 모듈 임포트: 딥러닝 프레임워크
        import torch.backends.cudnn as cudnn  # cuDNN 백엔드 임포트: GPU 가속
        from numpy import random  # NumPy의 random 모듈 임포트: 랜덤 숫자 생성
        
        from models.experimental import attempt_load  # 모델 로드 함수 임포트
        from utils.datasets import LoadStreams, LoadImages  # 데이터셋 로드 함수 임포트
        from utils.general import (  # 일반 유틸리티 함수 임포트
            check_img_size, 
            check_requirements, 
            check_imshow, 
            non_max_suppression, 
            apply_classifier, 
            scale_coords, 
            xyxy2xywh, 
            strip_optimizer, 
            set_logging, 
            increment_path
        )
        from utils.plots import plot_one_box  # 박스 그리기 함수 임포트
        from utils.torch_utils import (  # Torch 유틸리티 함수 임포트
            select_device, 
            load_classifier, 
            time_synchronized, 
            TracedModel
        )
        
        def detect(save_img=False):  # 객체 탐지 함수 정의
            # 파라미터 설정
            source, weights, view_img, save_txt, imgsz, trace = (
                opt.source, 
                opt.weights, 
                opt.view_img, 
                opt.save_txt, 
                opt.img_size, 
                not opt.no_trace
            )
            save_img = not opt.nosave and not source.endswith('.txt')  # 이미지 저장 여부
            webcam = source.isnumeric() or source.endswith('.txt') or source.lower().startswith(
                ('rtsp://', 'rtmp://', 'http://', 'https://')
            )  # 웹캠 여부 확인
        
            # 디렉토리 설정
            save_dir = Path(increment_path(Path(opt.project) / opt.name, exist_ok=opt.exist_ok))  # 결과 저장 디렉토리
            (save_dir / 'labels' if save_txt else save_dir).mkdir(parents=True, exist_ok=True)  # 라벨 디렉토리 생성
        
            # 초기화
            set_logging()  # 로깅 설정
            device = select_device(opt.device)  # 장치 선택 (CPU 또는 GPU)
            half = device.type != 'cpu'  # 반정밀도 FP16 사용 여부
        
            # 모델 로드
            model = attempt_load(weights, map_location=device)  # 모델 로드
            stride = int(model.stride.max())  # 모델의 stride 얻기
            imgsz = check_img_size(imgsz, s=stride)  # 이미지 크기 확인
        
            if trace:  # 모델을 tracing할지 여부
                model = TracedModel(model, device, opt.img_size)
        
            if half:  # 반정밀도 설정
                model.half()  # FP16으로 변환
        
            # 두 번째 단계 분류기 초기화 (사용되지 않음)
            classify = False
            if classify:
                modelc = load_classifier(name='resnet101', n=2)  # 분류기 초기화
                modelc.load_state_dict(torch.load('weights/resnet101.pt', map_location=device)['model']).to(device).eval()
        
            # 데이터 로더 설정
            vid_path, vid_writer = None, None  # 비디오 경로 및 작성기 초기화
            if webcam:  # 웹캠에서 데이터 로드
                view_img = check_imshow()  # 이미지 표시 여부 확인
                cudnn.benchmark = True  # 고정된 이미지 크기 속도 향상
                dataset = LoadStreams(source, img_size=imgsz, stride=stride)  # 스트림 로드
            else:  # 이미지 파일에서 데이터 로드
                dataset = LoadImages(source, img_size=imgsz, stride=stride)
        
            # 클래스 이름 및 색상 설정
            names = model.module.names if hasattr(model, 'module') else model.names  # 클래스 이름 얻기
            colors = [[random.randint(0, 255) for _ in range(3)] for _ in names]  # 클래스별 랜덤 색상 생성
        
            # 추론 실행
            if device.type != 'cpu':
                model(torch.zeros(1, 3, imgsz, imgsz).to(device).type_as(next(model.parameters())))  # 초기 실행
            old_img_w = old_img_h = imgsz  # 이전 이미지 크기 초기화
            old_img_b = 1  # 이전 배치 크기 초기화
        
            t0 = time.time()  # 시작 시간 기록
            for path, img, im0s, vid_cap in dataset:  # 데이터셋 반복
                img = torch.from_numpy(img).to(device)  # NumPy 이미지를 Torch 텐서로 변환
                img = img.half() if half else img.float()  # FP16 또는 FP32로 변환
                img /= 255.0  # 이미지 정규화 (0-255에서 0-1로)
                if img.ndimension() == 3:  # 차원이 3인 경우
                    img = img.unsqueeze(0)  # 배치 차원 추가
        
                # 워밍업
                if device.type != 'cpu' and (old_img_b != img.shape[0] or old_img_h != img.shape[2] or old_img_w != img.shape[3]):
                    old_img_b = img.shape[0]  # 이전 배치 크기 업데이트
                    old_img_h = img.shape[2]  # 이전 높이 업데이트
                    old_img_w = img.shape[3]  # 이전 너비 업데이트
                    for i in range(3):
                        model(img, augment=opt.augment)[0]  # 초기 실행을 통해 GPU 메모리 최적화
        
                # 추론
                t1 = time_synchronized()  # 시간 기록
                with torch.no_grad():  # 기울기 계산 비활성화 (메모리 누수 방지)
                    pred = model(img, augment=opt.augment)[0]  # 모델 예측
                t2 = time_synchronized()  # 시간 기록
        
                # 비최대 억제(NMS) 적용
                pred = non_max_suppression(pred, opt.conf_thres, opt.iou_thres, classes=opt.classes, agnostic=opt.agnostic_nms)
                t3 = time_synchronized()  # 시간 기록
        
                # 분류기 적용 (사용되지 않음)
                if classify:
                    pred = apply_classifier(pred, modelc, img, im0s)
        
                # 탐지 결과 처리
                for i, det in enumerate(pred):  # 이미지당 탐지 결과
                    if webcam:  # 웹캠인 경우
                        p, s, im0, frame = path[i], '%g: ' % i, im0s[i].copy(), dataset.count
                    else:  # 일반 이미지인 경우
                        p, s, im0, frame = path, '', im0s, getattr(dataset, 'frame', 0)
        
                    p = Path(p)  # 경로를 Path 객체로 변환
                    save_path = str(save_dir / p.name)  # 결과 이미지 경로
                    txt_path = str(save_dir / 'labels' / p.stem) + ('' if dataset.mode == 'image' else f'_{frame}')  # 결과 텍스트 경로
                    gn = torch.tensor(im0.shape)[[1, 0, 1, 0]]  # 정규화 게인 (너비, 높이)
                    if len(det):  # 탐지 결과가 있을 경우
                        # 바운딩 박스를 이미지 크기에 맞게 리스케일
                        det[:, :4] = scale_coords(img.shape[2:], det[:, :4], im0.shape).round()
        
                        # 결과 출력
                        for c in det[:, -1].unique():  # 클래스별로 결과 출력
                            n = (det[:, -1] == c).sum()  # 클래스별 탐지 개수
                            s += f"{n} {names[int(c)]}{'s' * (n > 1)}, "  # 문자열에 추가
        
                        # 결과 기록
                        for *xyxy, conf, cls in reversed(det):  # 바운딩 박스 정보
                            if save_txt:  # 텍스트 파일에 저장
                                xywh = (xyxy2xywh(torch.tensor(xyxy).view(1, 4)) / gn).view(-1).tolist()  # 정규화된 xywh
                                line = (cls, *xywh, conf) if opt.save_conf else (cls, *xywh)  # 라벨 포맷
                                with open(txt_path + '.txt', 'a') as f:  # 텍스트 파일 열기
                                    f.write(('%g ' * len(line)).rstrip() % line + '\n')  # 결과 기록
        
                            if save_img or view_img:  # 이미지에 바운딩 박스 그리기
                                plot_one_box(xyxy, im0, label=(
                                    f'{names[int(cls)]} {conf:.2f}' if save_txt else None), color=colors[int(cls)])  # 바운딩 박스 그리기
        
                    # 결과 표시
                    print(f"{s[:-2]}")  # 탐지 결과 출력
                    if view_img:  # 결과를 화면에 표시
                        cv2.imshow(str(p), im0)  # OpenCV로 이미지 표시
                        if cv2.waitKey(1) == ord('q'):  # 'q' 키 입력 시 종료
                            raise StopIteration
        
                    # 결과 저장
                    if save_img:  # 이미지 저장
                        if vid_cap:  # 비디오인 경우
                            if not vid_writer:  # 작성기가 없는 경우
                                fps = vid_cap.get(cv2.CAP_PROP_FPS)  # FPS 얻기
                                w = int(vid_cap.get(cv2.CAP_PROP_FRAME_WIDTH))  # 너비 얻기
                                h = int(vid_cap.get(cv2.CAP_PROP_FRAME_HEIGHT))  # 높이 얻기
                                save_path = str(save_dir / f'{p.stem}.mp4')  # 비디오 저장 경로
                                vid_writer = cv2.VideoWriter(save_path, cv2.VideoWriter_fourcc(*'mp4v'), fps, (w, h))  # 비디오 작성기 초기화
                            vid_writer.write(im0)  # 비디오에 이미지 기록
                        else:  # 이미지인 경우
                            cv2.imwrite(save_path, im0)  # 이미지 저장
        
            # 비디오 작성기 종료
            if vid_writer:  # 비디오 작성기가 있을 경우
                vid_writer.release()  # 비디오 작성기 종료
        
            # 총 소요 시간 출력
            print(f"Done. ({time.time() - t0:.3f}s)")  # 총 소요 시간 출력
        
        if __name__ == "__main__":  # 스크립트가 직접 실행될 때
            parser = argparse.ArgumentParser()  # 인자 파서 초기화
            # 파라미터 추가
            parser.add_argument('--weights', type=str, default='yolov5s.pt', help='model.pt path')  # 모델 경로
            parser.add_argument('--source', type=str, default='data/images', help='source')  # 소스 경로
            parser.add_argument('--img-size', type=int, default=640, help='inference size (pixels)')  # 이미지 크기
            parser.add_argument('--conf-thres', type=float, default=0.25, help='confidence threshold')  # 신뢰도 임계값
            parser.add_argument('--iou-thres', type=float, default=0.45, help='NMS IoU threshold')  # NMS IoU 임계값
            parser.add_argument('--view-img', action='store_true', help='show results')  # 결과 표시 여부
            parser.add_argument('--save-txt', action='store_true', help='save results to *.txt')  # 텍스트 저장 여부
            parser.add_argument('--save-img', action='store_true', help='save inference images')  # 이미지 저장 여부
            parser.add_argument('--nosave', action='store_true', help='do not save images/videos')  # 저장 안 함 옵션
            parser.add_argument('--project', default='runs/detect', help='save results to project/name')  # 프로젝트 이름
            parser.add_argument('--name', default='exp', help='project/name')  # 이름
            parser.add_argument('--exist-ok', action='store_true', help='existing project/name ok, do not increment')  # 기존 이름 처리 여부
            parser.add_argument('--classes', nargs='+', type=int, help='filter by class: --class 0, or --class 0 2 3')  # 클래스 필터
            parser.add_argument('--agnostic-nms', action='store_true', help='class-agnostic NMS')  # 클래스 무관 NMS 여부
            parser.add_argument('--augment', action='store_true', help='augmented inference')  # 증강 추론 여부
            parser.add_argument('--device', default='', help='device id (i.e. 0 or 0,1 or cpu)')  # 장치 ID
            parser.add_argument('--no-trace', action='store_true', help='don\'t trace model')  # 모델 트레이스 여부
            opt = parser.parse_args()  # 인자 파싱
            print(opt)  # 파싱 결과 출력
            detect()  # 탐지 함수 호출
        
        ```
        
    1. weights: 가중치 파일 지정 ; [yolov7-tiny.pt](http://yolov7-tiny.pt) 모델의 가중치 사용 
    2. img: 입력 이미지 크기 설정 ; 640x640으로 조정한 후 탐지 수행
    3. conf: 탐지 신뢰도 기준 설정 ; 0.25 이상의 신뢰도 가진 탐지 결과만 출력 
    4. source: 탐지할 이미지가 있는 경로 설정 “inference/images”
        
        ![image (24)](https://github.com/user-attachments/assets/fe8e37e7-dd5c-4c95-a4b3-f3dabb76566a)
        
    - 객체 탐지 정상 수행
        
        ![image (25)](https://github.com/user-attachments/assets/748d2f9e-2efa-453e-99ad-ad9478f2a4e2)
        
4. yaml 파일 작성
    - 클래스 이름 리스트 형식으로 작성
    
    ![image (26)](https://github.com/user-attachments/assets/fbf7c7d7-6f2b-4ae3-b666-e1091c13b3eb)
    
5. 훈련 수행
    
    ```python
    !python train.py --img 320 --batch 8 --epochs 20 --data vocdata.yaml --weights yolov7-tiny.pt --cache
    ```
    
    - —img 320: 모든 입력 이미지 크기를 320x320으로
    - —batch 8: 배치크기 8 ; 한 번의 훈련 단계에서 8개 이미지 처리
    - —epochs 20: 훈련 20에폭 동안 수행 ; 에폭 = 전체 데이터셋 한 번 훈련하는 과정
    - —data: 훈련에 사용할 데이터셋 설정 ; vocdata.yaml 데이터 경로, 클래스 수 등의 정보 포함
    - —weights:  초기 가중치 파일을 지정 ; `yolov7-tiny.pt`는 YOLOv7 모델의 경량화된 버전의 가중치 파일
        
        weights(모델 파라미터)나 cfg(네트워크 ; 레이어, 활성화 함수 등) 둘 중 하나 반드시 입력해야 함 
        
    - —cache: 훈련 시 데이터를 빠르게 로드하기 위해 (훈련 시마다 데이터셋을 다시 읽지 않도록)
6. weight 파일 다운로드 
7. 윈도우에서 YOLOv7 추론 실습 
    - 실습 파일, 라이브러리는 이미 다운받은 상태
    - yolov7 저장소 복제
        
        ```python
        ynu@ynu-ROKEY:~/DeepLearning4Projects/Chap7$ git clone https://github.com/WongKinYiu/yolov7
        'yolov7'에 복제합니다...
        remote: Enumerating objects: 1197, done.
        remote: Total 1197 (delta 0), reused 0 (delta 0), pack-reused 1197 (from 1)
        오브젝트를 받는 중: 100% (1197/1197), 74.23 MiB | 4.17 MiB/s, 완료.
        델타를 알아내는 중: 100% (519/519), 완료.
        ```
        
    - 이번에도 훈련 다 안 돌려서, yolov7 파일에 yolov7_tiny_voc.pt 가중치 파일 복사
        
        ```python
        ynu@ynu-ROKEY:~/DeepLearning4Projects/Chap7$ cp yolov7_tiny_voc.pt yolov7/
        ```
        
        ![image (27)](https://github.com/user-attachments/assets/8f62837c-912d-4bcc-b78c-fe4fa19fa577)
        
    - chap7/data에 있는 영상 추론
        
        ```python
        cd DeepLearning4Projects/Chap7/yolov7
        detect.py --weights yolov7_tiny_voc.pt --img 640 --conf 0.25 --source ../data/crosswalk_cctv_01.mp4 --view-img
        ```
        
        ![image (28)](https://github.com/user-attachments/assets/727c382e-b446-46a3-87bd-0cccd09c5c52)
        
        ![image (29)](https://github.com/user-attachments/assets/9e53a2db-f594-4d3d-beda-6268855c4962)
        
    - 이것도 —source 이후에 0을 입력하면 웹캠으로부터 영상 받아서 추론
        
        ```python
        python3 detect.py --weights yolov7_tiny_voc.pt --img 640 --conf 0.25 --source 0 --view-img
        ```
        
        → 이땐 너무 가까이 있는 물체를 인식 못하는 줄 알았는데, 집에서 다시 해보니까 인식 잘 됨..
