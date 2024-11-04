### <span style="color: red;">Only Colab</span>

```bash
# 구글 드라이브 연결
from google.colab import drive
drive.mount('/content/gdrive')
```

```bash
# 구글 드라이브 디렉토리 확인 및 이동
# !ls /content/gdrive
!ls
```

```bash
%cd /content/gdrive/MyDrive/ssd/
```

---
### <span style="color: rgb(0, 0, 255);">Required!!!</span>

```bash
# 파이토치 SSD 실습 소스 파일들 다운로드
!git clone https://github.com/jetsonai/pytorch-ssd
!ls
```

```bash
%cd pytorch-ssd
!ls
```

---
### <span style="color: rgb(0, 0, 255);">Download Pre-Model (MobileNet V1)</span>

```bash
# 파이토치 SSD MobileNetV1 미리 학습된 모델 파일 다운로드
!wget https://nvidia.box.com/shared/static/djf5w54rjvpqocsiztzaandq1m3avr7c.pth -O models/mobilenet-v1-ssd-mp-0_675.pth
```

---
### <span style="color: rgb(0, 0, 255);">Install Package</span>

```bash
# 필수 패키지 설치
!pip3 install -v -r requirements.txt
```

# 목차
## 1. Download Image Data
## 2. 데이터 셋 Class(OpenImagesDataset) 
## 3. MobileNet V1
## 4. Train Object Detection MobileNet SSD

---
### <span style="color: rgb(0, 0, 255);">Download Image Data</span>
1. **open_images_downloader.py**    
    - 해당 파일을 통해 구글에서 제공하는 Open Images Dataset이미지를 다운로드  

2. **--class-names "Person, Car, Bus**  
   - Open Images Dataset에는 총 601개의 class가 있고 그 중에서 3개만 선택  
   - 다운로드할 이미지의 class를 지정  
   - 여기서는 "Person", "Car", "Bus" class의 이미지를 다운로드   

3. **--data=data/cctv**  
   - 다운로드한 이미지와 관련 데이터를 저장할 디렉토리를 지정  
   - 여기서는 "data/cctv" 폴더에 저장 됨  

4. **--max-images=4500**  
   - 다운로드할 최대 이미지 수를 지정  
   - 전체 class에 대해 총 4500개의 이미지를 다운로드 (최대 5000개까지 내려 받을 수 있음)  


5. **--num-workers=2**  
   - 다운로드 작업에 사용할 워커(작업자) 수를 지정    
   - 2개의 워커를 사용하여 병렬로 다운로드를 수행    
  

```bash
# CCTV에서 감시하고자 하는 대상인 사람, 차, 버스 데이터를 다운로드
!python3 open_images_downloader.py --class-names "Person, Car, Bus" --data=data/cctv --max-images=4500 --num-workers=2
```

- PyTorch-ssd/data/cctv.. 디렉토리 구조

---
### <span style="color: rgb(0, 0, 255);">데이터 셋 Class(OpenImagesDataset)</span>

```python
# 오픈 이미지 데이터세트를 가져와 이미지와 라벨을 관리해주는 Class
class OpenImagesDataset:

    # 이미지 데이터 셋 초기화, 이미지 전처리 등 담당
    def __init__(self, root,
                    transform=None, target_transform=None,
                    dataset_type="train", balance_data=False):
        self.root = pathlib.Path(root)
        self.transform = transform
        self.target_transform = target_transform
        self.dataset_type = dataset_type.lower()

        self.data, self.class_names, self.class_dict = self._read_data()

        self.balance_data = balance_data
        self.min_image_num = -1
        if self.balance_data:
            self.data = self._balance_data()
        self.ids = [info['image_id'] for info in self.data]

        self.class_stat = None

    ...

```

```python
class OpenImagesDataset:
    ...

    def _read_data(self):
        # annotations-bbox.csv에서 pandas를 사용하여 바운딩 박스값과 class정보를 가져옴
        annotation_file = f"{self.root}/sub-{self.dataset_type}-annotations-bbox.csv"
        logging.info(f'loading annotations from: {annotation_file}')
        annotations = pd.read_csv(annotation_file)
        logging.info(f'annotations loaded from:  {annotation_file}')
        class_names = ['BACKGROUND'] + sorted(list(annotations['ClassName'].unique()))
        class_dict = {class_name: i for i, class_name in enumerate(class_names)}
        data = []
        for image_id, group in annotations.groupby("ImageID"):
            img_path = os.path.join(self.root, self.dataset_type, image_id + '.jpg')
            if os.path.isfile(img_path) is False:
                 logging.error(f'missing ImageID {image_id}.jpg - dropping from annotations')
                 continue
            boxes = group.loc[:, ["XMin", "YMin", "XMax", "YMax"]].values.astype(np.float32)
            # make labels 64 bits to satisfy the cross_entropy function
            labels = np.array([class_dict[name] for name in group["ClassName"]], dtype='int64')
            #print('found image {:s}  ({:d})'.format(img_path, len(data)))
            data.append({
                'image_id': image_id,
                'boxes': boxes,
                'labels': labels
            })
        print('num images:  {:d}'.format(len(data)))
        return data, class_names, class_dict

    ...
```

```python
class OpenImagesDataset:
    ...

    # 데이터의 index를 입력받고 이미지 데이터와 바운딩 박스의 좌표 정보, 라벨 정보 반환
    def _getitem(self, index):
        image_info = self.data[index]
        # 이미지 읽어오기
        image = self._read_image(image_info['image_id'])

        """
        바운딩 박스, 라벨 복사는 원본 데이터 수정하지 않고 사용하려고 복사한듯
        _getitem메소드 내부에서만 사용됨
        """

        # 데이터 셋 보존을 위한 박스 객체 복사
        # 바운딩 박스는 Xmin, Ymin, Xmax, Ymax 형태로 되어 있음
        boxes = copy.copy(image_info['boxes'])
        boxes[:, 0] *= image.shape[1]
        boxes[:, 1] *= image.shape[0]
        boxes[:, 2] *= image.shape[1]
        boxes[:, 3] *= image.shape[0]
        # 데이터 셋 보존을 위한 라벨 객체 복사
        labels = copy.copy(image_info['labels'])

        # 데이터 증강 기법 사용
        # 실제 적용된 증강 기법은 train_ssd.py에서 확인 할 수 있다.
        if self.transform:
            # 이미지 데이터 증강
            # 이전에 이미지 분류에서 하던 이미지 변환작업을 여기서 수행
            image, boxes, labels = self.transform(image, boxes, labels)
        if self.target_transform:
            # 바운딩 박스, 라벨 변환 
            """
            - 주요 변환점
            1. 데이터 셋은 corner_form으로 되어 있음 SSD는 corner_form을 사용하기 때문에
                여기서 변환해줌 즉, 최대, 최소 좌표에서(xmin, ymin, xmax, ymax) -> 중심 좌표 형식으로(center_x, center_y, width, height)

            2. Prior Box(Anchor Box)와 Ground Truth(GT) 바운딩 박스를 매칭시켜, 모델이 학습할 수 있는 바운딩 박스의 좌표와 라벨을 반환

            - Prior Box: SSD와 같은 객체 탐지 모델에서 Prior Box는 미리 정의된 고정된 크기와 위치의 박스들, Prior Box는 다양한 위치와 크기에서 설정
                , Prior Box 자체는 어떤 객체와도 관련없음 그저 모델이 추론할 때 사용할 박스들일 뿐임
                , 모델은 학습을 통해 각 Prior Box가 특정한 물체와 얼마나 잘 맞는지를 학습해야 함
            
            - Ground Truth 바운딩 박스의 역할: Ground Truth 바운딩 박스는 이미지에 실제로 존재하는 객체를 사람이 미리 정의한 정답 데이터
                객체의 정확한 위치와 크기를 나타내며, 각 박스에는 객체의 종류를 나타내는 라벨이 함께 들어있고 이 데이터를 Prior Box와 매칭하여 학습에 사용.

            boxes: Prior Box와 Ground Truth 박스를 매칭한 후, Prior Box 좌표와 Ground Truth 좌표의 차이를 계산한 값
                    , 이 값이 추후에 모델이 예측해야 하는 값으로 사용됨
            labels: 각 Prior Box가 어떤 class를 나타내는지(위 labels는 Ground Truth 클래스 정보를 나타내고 이걸 사용함)

            
            MatchPrior에서 box_utils.assign_priors로 postive/ nagative 계산
            1. box_utils.assign_priors:
                이 함수에서 Prior Box와 Ground Truth 바운딩 박스 간의 IoU를 계산하고, 임계값에 따라 Positive/Negative 매칭을 수행
                Positive로 매칭된 Prior Box에 Ground Truth의 좌표 및 라벨을 할당
                Negative 매칭된 박스에는 배경 라벨(일반적으로 0)을 할당
            2. 좌표 변환:
                매칭된 Prior Box 좌표를 중심 좌표 형식으로 변환한 후, 최종적으로 모델이 학습할 수 있도록 정규화된 형식으로 변환
            """
            boxes, labels = self.target_transform(boxes, labels)
        return image_info['image_id'], image, boxes, labels 

    def __getitem__(self, index):
        # index로 _getitem메소드 호출
        _, image, boxes, labels = self._getitem(index)
        # 이미지 데이터, 바운딩 박스의 좌표 정보, 라벨 정보 반환
        return image, boxes, labels
    
    ...

```

- Prior Box(Anchor Box)와 Ground Truth(GT) 바운딩 박스 예  

<img width="752" alt="image" src="https://github.com/user-attachments/assets/3cbcd40e-bffa-4286-9028-077b975484d8">  

<img width="751" alt="image" src="https://github.com/user-attachments/assets/952ebb02-d3a7-496a-9a07-ae2880edd01a">

- 판단 방법

IOU( = Jaccard overlaps )값으로 결정하며, 특정값( ex. 0.5)을 정하여 판단  
이 척도는 bounding box와 prior 사이에서 위 그림처럼 연산을 하여 값을 추출  
이 값이 1에 가까울수록 서로 겹치는 영역이 많아진다는 것을 알 수 있음  

기본적인 모델에서는 IOU값이 0.5 ( 특정값을 0.5로 설정 ) 이상인 값들은 prior내 객체가 있다고 판정하여 positive matching,  
0.5이하의 값들은 prior내 객체가 없다고 판정하여 negative matching을 하게 됨  

학습시 0.5이상인 것들만 사용한다.  

<img width="751" alt="image" src="https://github.com/user-attachments/assets/8be98b0b-ffd0-434a-9f00-8b147e54d229">  

매칭된 bounding box들을 regression하여 학습  
뭘 학습하냐? <span style="color: rgb(255, 0, 0);">이 bounding box(Anchor box)를 움직여서 GT와 최대한 일치시키는 것이다.</span>  


---
### <span style="color: rgb(0, 0, 255);">MobileNet V1</span>
실습에서 MobileNet을 사용하는 이유 (교재를 참고한 내 생각)
- 높은 정확도: MobileNet은 경량화된 네트워크임에도 불구하고 준수한 정확도를 유지  
- 낮은 계산 복잡도: MobileNet은 Depthwise Separable Convolutions를 사용하여 계산 복잡도를 크게 줄임  
- 낮은 에너지 소비: 모델의 경량화로 인해 전력 소모가 적어, 에너지 효율성이 높음  
- 작은 모델 사이즈: 모델의 파라미터 수와 크기가 작아 저장 공간과 메모리 사용이 적다.  

이러한 특성 덕분에 MobileNet은 임베디드 보드와 같은 낮은 하드웨어 사양의 장치에서 사용하기 적합함   
하드웨어 성능과 모델의 정확도 사이의 트레이드오프를 고려하여, 효율적인 성능과 충분한 정확도를 제공하기 때문에 사용했다고 생각 됨
#### Depthwise Separable Convolution  
depthwise convolution과 pointwise convolution을 결합한 형태   
이 기법은 convolution 연산량을 크게 줄이기 때문에 모델의 크기 역시 줄어들게 된다.  
##### 기존 Convolution 구조  
: 일반적인 convolution의 형태는 다음 그림과 같다.   
filter 하나는 input data의 channel 수만큼 존재하며 모든 channel에 대해 합성곱을 함

<img src="https://github.com/user-attachments/assets/472e5ba9-62b0-45ed-a840-4d5901624c8a" alt="Depthwise Convolution" width="1000">

<img src="https://github.com/user-attachments/assets/a076e06c-af45-4514-96a3-d2c4c640fef0" alt="Depthwise Convolution" width="1000">

기본적으로 하나의 Convolution을 통과하면 하나의 Feature Map(Activation Map)이 생성된다.   
이때 Feature Map 한 개를 생성하는데 Kernel Size x Kernel Size x Input Channel(입력 채널 수)의 파라미터(Weight)가 필요함 

구체적으로, 커널이 입력 이미지 또는 입력 텐서의 각 채널에 대해 개별적으로 작용하며, 모든 채널의 결과를 합산하여 하나의 Feature Map을 생성  
따라서, 하나의 커널은 각 채널마다 별도의 가중치를 학습하고, 그 결과들을 결합하여 최종적으로 Feature Map을 만든다.  

예시:  
Kernel Size: 3 * 3   
Input Channel: 3 (color 이미지일 경우)  

이 경우, 하나의 Feature Map을 생성하기 위해  3 * 3 * 3 = 27 개의 파라미터가 필요함    
각 Feature Map은 이러한 계산을 거쳐 학습되며, 만약 여러 개의 Feature Map을 출력하고자 한다면, 그 수에 비례하여 파라미터 수가 늘어남  

예를 들어 3개의 Feature Map을 출력하고자 한다면  
3 * 3 * 3 = 27 에 3를 곱하여  3 * 3 * 3 * 3 = 81개의 파라미터가 필요하게 됨  
##### Depthwise Convolution  
<img src="https://github.com/user-attachments/assets/22fc5c4a-8639-4c82-9954-df818641426a" alt="Depthwise Convolution" width="1000">
<img src="https://github.com/user-attachments/assets/e2543879-46da-4234-8764-de9a789a349d" alt="Depthwise Convolution" width="1000">

Depth-wise Convolution은 일반적인 컨볼루션과는 다르게 작동함  
Depth-wise Convolution은 한 번 통과하면, 하나로 병합되지 않고, 채널이 각각 Feature Map이 됨  
하나의 Feature Map = Kernel Size x Kernel Size  

이 방식에서는 입력 이미지의 각 채널을 독립적으로 처리한다.   
즉, 3채널 RGB 이미지를 입력으로 받았다면, 각 채널에 대해 별도의 2D 컨볼루션 연산을 수행   
이로 인해 입력 채널 수와 동일한 수의 Feature Map이 생성된다.   
예를 들어, 3채널 입력 이미지에 Depth-wise Convolution을 적용하면, 3개의 개별 Feature Map이 출력   
이는 일반 컨볼루션에서 모든 채널의 정보를 합쳐 하나의 Feature Map을 만드는 것과는 대조적이다.  
 
Depth-wise Convolution에서는 각 채널마다 별도의 커널을 사용하므로, 전체 파라미터 수는 Kernel Size x Kernel Size x Input Channel이 된다.   
3x3 커널을 사용하는 3채널 이미지의 경우, 총 27개의 파라미터가 필요, 각 Feature Map당 파라미터 수는 Kernel Size x Kernel Size 이므로 9개 이다.  

Depth-wise Convolution에서는 출력 Feature Map의 수는 입력 채널 수와 동일하기 때문에  
color 이미지 기준으로 채널 3개(RGB) 일때   
Feature Map1 = 3 * 3  
Feature Map2 = 3 * 3  
Feature Map3 = 3 * 3  
R = Feature Map1, G = Feature Map2, B = Feature Map3  이므로  총 27개 파라미터가 필요하게 됨  

이 방식의 주요 장점은 파라미터 수와 연산량을 크게 줄일 수 있다는 것    

기본 convolution연산과의 차이점으로   
기존 convolution은 하나의 필터를 통해 결합되어, 입력 채널들 간의 관계를 고려한 Feature Map을 생성함   
즉, 입력의 모든 채널에 대해 하나의 필터(커널)을 적용   
예를 들어, 이미지의 각 채널(예: RGB 채널)이 서로 다른 색상 정보를 제공할 때, 기본 컨볼루션은 이 색상 정보들을 통합해 최종 Feature Map을 만든다.  

depth-wise convolution의 경우 입력 채널마다 독립적인 커널을 사용하여 각 채널별로 Feature Map을 만듬  
각 채널의 정보가 독립적으로 처리되어 각 채널에 대해 별도의 Feature Map이 생성  
이후 이 Feature Map들을 결합(concat)하여 최종적으로 하나의 Feature Map을 만드는 경우, 채널 간의 정보는 직접적으로 결합되지 않고,   
채널마다 독립적인 정보만 유지됨 (기본 컨볼루션과 달리 채널 간 상호작용이 일어나지 않는다.)   
입력 채널의 정보를 따로따로 처리하면서 각 채널의 고유한 특징을 강조하며   
예를 들어, RGB 이미지의 경우, 각 채널(R, G, B)의 특징을 각각 추출하고, 채널 간 결합을 하지 않은 채로 각 채널만의 정보를 처리한다.  

이러한 독립적인 처리 방식은 연산량이 적고, 채널 간 상호작용 없이 독립적인 특징을 유지하며 빠르게 처리할 수 있지만  
딥러닝 모델의 성능은 다양한 특징들의 복잡한 조합에 의해 결정되는데,  
예를 들어 RGB 이미지에서 색상 정보와 엣지 정보를 결합하여 더 복잡한 객체 특징을 파악할 수 있다.  
그러나 Depth-wise Convolution만으로는 이러한 채널 간 상호작용을 모델링하지 못해 복잡한 패턴을 학습하는 데 제한이 생길 수 있음  
따라서 채널 간 정보를 통합하고 더 높은 수준의 특징을 추출하고   
채널 간의 정보 통합을 위해 추가적으로 Point-wise Convolution을 수행한다.   

##### Point-wise Convolution  
1 by 1 Convolution연산을 왜 하는지?  
1. Channel 수 조절
2. 연산량 감소(Efficient)
3. 비선형성(Non-linearity)

여기서 볼건 2번 연산량 감소 효과를 기대함  

<img src="https://github.com/user-attachments/assets/2c3c28c6-1137-400e-bcae-35faca507835" alt="Depthwise Convolution" width="1000">

일반적인 5x5 컨볼루션을 그냥 진행한다면 28 x 28 x 32 x 5 x 5 x 192 = 120,422,400 약 1.2억 번의 연산이 필요  

<img src="https://github.com/user-attachments/assets/d546316f-c546-45e0-b7b0-01ef1aaebe13" alt="Depthwise Convolution" width="1000">  

첫번째로 채널을 줄이는 연산량은 28 x 28 x 16 x 1 x 1 x 192 = 2,408,448 약 240만 번의 연산이 필요하며,  
다시 32 채널로 늘릴때는 28 x 28 x 32 x 5 x 5 x 16 =  10,035,200 약 1000만번의 연산이 필요  

따라서, 총 약 1240만번의 연산이 필요하다.  

최종 결과는 같은 5x5 필터를 사용했지만  1.2억과 1240만의 차이가 발생  

Convolution연산에서 채널수는 원하는만큼 정할 수 있음 하지만, 이렇게 되면 학습시 파라미터 개수가 커지는 문제가 생김  
1 * 1 convolution을 해줌으로서 파라미터 수를 과하게 증가시키지 않으므로 Channel 수를 특정 경우가 아닌 이상 마음껏 조절할 수 있고,   
다양한 크기를 가진 Convolution Layer를 통해 우리가 원하는 구조의 모델을 구성해 볼 수 있다.  
#### MobileNet V1 네트워크 구조
<img src="https://github.com/user-attachments/assets/0bc535ce-c8d6-419c-b0f2-6bb008f2473e" alt="Depthwise Convolution" width="700" height="800">

```python
# mobilenet.py
class MobileNetV1(nn.Module):
    def __init__(self, num_classes=1024):
        super(MobileNetV1, self).__init__()

    '''기본 Convolution 구조'''
    def conv_bn(inp, oup, stride):
            return nn.Sequential(
                nn.Conv2d(inp, oup, 3, stride, 1, bias=False),
                nn.BatchNorm2d(oup),
                nn.ReLU(inplace=True)
            )

    '''Depth-wise convolution 와 Point-wise convolution'''
    def conv_dw(inp, oup, stride):
        return nn.Sequential(
            '''Depth-wise Convolution'''
            nn.Conv2d(inp, inp, 3, stride, 1, groups=inp, bias=False),
            nn.BatchNorm2d(inp),
            nn.ReLU(inplace=True),

            '''Point-wise Convolution'''
            nn.Conv2d(inp, oup, 1, 1, 0, bias=False),
            nn.BatchNorm2d(oup),
            nn.ReLU(inplace=True),
        )

    self.model = nn.Sequential(
        '''convolution'''
        conv_bn(3, 32, 2),

        '''Depth-wise Separable Convolution blocks'''
        conv_dw(32, 64, 1),
        conv_dw(64, 128, 2),
        conv_dw(128, 128, 1),
        conv_dw(128, 256, 2),
        conv_dw(256, 256, 1),
        conv_dw(256, 512, 2),
        conv_dw(512, 512, 1),
        conv_dw(512, 512, 1),
        conv_dw(512, 512, 1),
        conv_dw(512, 512, 1),
        conv_dw(512, 512, 1),
        conv_dw(512, 1024, 2),
        conv_dw(1024, 1024, 1),
    )

    '''Fully connected layer(FCM)'''
    self.fc = nn.Linear(1024, num_classes)

    ...
```

---
### <span style="color: rgb(0, 0, 255);">Train Object Detection MobileNet SSD</span>
#### 1. History
  
Object Detection 분야에 딥러닝을 최초로 적용시킨 모델이 2013년 11월에 등장하는데 그 모델이 바로   
R-CNN(Regions with Convolutional Neuron Networks features)임  

분명 기존의 다른 모델과 비교해 성능을 상당히 향상시킨 모델이였지만 처리속도가 매우 느려서 Real-Time에서 활용하기 어려웠다.  
(실제로 이미지 한장당 GPU환경에서는 13초가 걸렸으며 CPU로는 53초가 걸림)    

이후 수많은 Deep Learning 이용한 모델들이 등장하기 시작하는데, 그들의 고민 중 하나가 바로 처리속도 였음.  

#### 2. SSD(Single Shot Multibox Detector)
YOLO는 실시간 처리 속도에서 뛰어났으나, 정확도 면에서는 한계가 있었으며, 특히 작은 물체를 잘 감지하지 못하는 문제가 있었다.  
(YOLO의 문제점은 입력 이미지를 7x7 크기의 그리드로 나눈 후, 각 그리드별로 Bounding Box를 예측하기 때문에,  
그리드 크기보다 작은 물체는 제대로 감지하지 못하는 한계가 있었다.)  

SSD는 이러한 한계를 극복하기 위한 시도에서 출발한 모델이다.  

SSD의 주요 특징은 다음과 같다:  
1. Multi-Scale Feature Maps for Detection: 다양한 크기의 Feature Map을 활용하여 물체의 크기에 상관없이 감지 가능.  
2. Default Boxes Generation: 다양한 비율과 크기의 Default Box(Prior Box)를 사용해 여러 형태의 객체를 감지할 수 있도록 함.  

이 모델은 다음과 같은 방식으로 YOLO와 차별화 됨  
<span style="color: rgb(255, 0, 0);">SSD는 Fully Convolutional Network(FCN)와 유사하게, 앞단의 컨볼루션 Feature Map을 활용해 세밀한 디테일을 잡아내고,</span>  
Faster R-CNN의 Anchor Box 개념을 도입해 다양한 형태와 크기의 물체를 효율적으로 감지할 수 있도록 한다.  

뭔 말이냐?

<img src="https://github.com/user-attachments/assets/9f704712-f018-46f7-8800-1fae9eabc786" alt="Depthwise Convolution" width="1000">  

위 이미지를 보면 고양이와 개 객체에서 고양이는 개보다 상대적으로 작은 스케일을 가짐  
conv layer들 중에서 예를 들어 8 * 8 사이즈를 갖는 앞단 layer에서 상대적으로 작은 고양이 객체를 찾게 됨  
그 이후 뒤쪽 layer인 4 * 4 feature map을 통해 상대적으로 큰 강아지 객체를 잡아내게 된다.  

즉, 앞단에서 작은 객체를 잡아내고, 뒷단에서 큰 객체를 잡아낸다고 이해하면 된다.  

각 feature map에서 잡아내지 못한 객체는 background로 처리   

입력 이미지 --> MobileNet (이미지의 특징만 추출) --> Feature Map (작은 객체 탐지)   
--> Extra Layer 1 --> 작은 Feature Map (중간 크기 객체 탐지)  
--> Extra Layer 2 --> 더 작은 Feature Map (큰 객체 탐지)  
--> Extra Layer 3 --> 매우 작은 Feature Map (더 큰 객체 탐지)  

```python
class SSD(nn.Module):
    def __init__(self, num_classes: int, base_net: nn.ModuleList, source_layer_indexes: List[int],
                 extras: nn.ModuleList, classification_headers: nn.ModuleList,
                 regression_headers: nn.ModuleList, is_test=False, config=None, device=None):
        """
        num_classes: 탐지할 객체의 클래스 수
        base_net: MobileNetV1 모델을 포함한 기본 네트워크
        source_layer_indexes: 다양한 크기의 feature map을 추출하기 위한 레이어 인덱스 리스트
        extras: 추가적인 컨볼루션 레이어로 구성된 ModuleList로, 더 많은 feature map을 생성
        classification_headers: 각 anchor box에 대한 클래스 확률을 예측하는 헤더
        regression_headers: 각 anchor box에 대한 바운딩 박스 위치를 회귀하는 헤더들
        is_test: 모델이 테스트 모드인지 여부를 나타내는 불리언 값
        config: 모델 설정에 대한 다양한 하이퍼파라미터를 포함
        """
        super(SSD, self).__init__()

        self.num_classes = num_classes
        self.base_net = base_net
        self.source_layer_indexes = source_layer_indexes
        self.extras = extras # 추가 레이어
        self.classification_headers = classification_headers # 분류 헤더
        self.regression_headers = regression_headers # 회귀 헤더
        self.is_test = is_test
        self.config = config

    ...

    def forward(self, x: torch.Tensor) -> Tuple[torch.Tensor, torch.Tensor]:
        confidences = []
        locations = []
        start_layer_index = 0
        header_index = 0

        ...

        '''base_net의 앞쪽 레이어들이 작은 feature map을 생성하여 작은 객체(고양이)를 감지'''
        for end_layer_index in self.source_layer_indexes:
            # 특정 layer에서 feature map을 처리하는 코드
            ...
            for layer in self.base_net[start_layer_index: end_layer_index]:
                x = layer(x)  # base_net(MobileNet v1)을 통한 feature map 생성
            ...
            # SSD의 탐지 과정: 여기서 탐지를 수행 (classification(객체 예측) & regression(바운딩 박스 예측))
            confidence, location = self.compute_header(header_index, y)  # feature map을 이용한 물체 감지
            header_index += 1
            confidences.append(confidence)
            locations.append(location)
        
        '''extras 레이어들은 더 작은 크기의 feature map을 생성하여 큰 객체(강아지)를 감지'''
        for layer in self.extras:  # extras 레이어에서 추가 feature map 생성
            x = layer(x)
            # SSD의 탐지 과정: 여기서 탐지를 수행 (classification(객체 예측) & regression(바운딩 박스 예측))
            confidence, location = self.compute_header(header_index, x)  # feature map을 이용한 물체 감지
            header_index += 1
            confidences.append(confidence)
            locations.append(location)
            
        confidences = torch.cat(confidences, 1)
        locations = torch.cat(locations, 1)

        if self.is_test:
            confidences = F.softmax(confidences, dim=2)
            boxes = box_utils.convert_locations_to_boxes(locations, self.priors, self.config.center_variance, self.config.size_variance)
            boxes = box_utils.center_form_to_corner_form(boxes)
            return confidences, boxes
        else:
            return confidences, locations
        
    '''분류 헤더로서 클래스 신뢰도, 회귀 헤더에서 위치를 계산함'''
    def compute_header(self, i, x):
        # 두개의 예측은 index로 접근하여 수행
        # 전체 class에 대한 데이터로 class예측
        confidence = self.classification_headers[i](x)
        confidence = confidence.permute(0, 2, 3, 1).contiguous()
        confidence = confidence.view(confidence.size(0), -1, self.num_classes)

        # 바운딩 박스 좌표로 x, y, w, h 이 4개의 좌표를 예측함 
        location = self.regression_headers[i](x)
        location = location.permute(0, 2, 3, 1).contiguous()
        location = location.view(location.size(0), -1, 4)

        return confidence, location
    
    ...

```

#### 3. MobileNet SSD  
MobileNet은 모바일 및 임베디드 비전 애플리케이션을 위해 설계된 경량 심층 신경망 아키텍처이다.   
현실에서 사용되는 실제 응용 프로그램은 제한된 하드웨어(Raspberry Pi, 스마트폰 등)에서 실시간으로 실행되어야 한다는 요구가 있으며,  
이를 충족하기 위해 Google에서 2017년에 개발되었다.  

비슷한 시기(2016년), Google 연구팀은 정확도를 크게 떨어뜨리지 않으면서도 임베디드 장치에서 실시간으로   
실행 가능한 객체 감지 모델에 대한 필요를 충족하기 위해 SSD(Single Shot Multibox Detector)를 개발하였다.  

두 연구 모두 저사양 장치에서 리소스가 많이 소모되고 전력 소모가 큰 신경망을 효율적으로 실행하려는 공통의 목표를 가졌으며,  
결과적으로 MobileNet이 SSD에서 기본 네트워크(backbone)로 사용되면서 MobileNet SSD가 탄생하게 되었다.  
(SSD는 기본 네트워크와 독립적으로 설계되었기 때문에 VGG, MobileNet과 같은 다양한 네트워크 위에서 실행될 수 있다.)  


<img src="https://github.com/user-attachments/assets/159e9706-5f2e-4d79-b9dc-3786fd10826d" alt="Depthwise Convolution" width="1000"> 

```bash
# Mobilenetv1_ssd.py
def create_mobilenetv1_ssd(num_classes, is_test=False):
    base_net = MobileNetV1(1001).model  # disable dropout layer

    '''
    MobileNet의 12번째 레이어와 14번째 레이어에서 각각 feature map을 추출하여 사용
    다양한 크기의 객체를 효과적으로 탐지하기 위함
    12, 14번째 layer에서 추출한 feature map을 각각 ssd에서 사용함
    '''
    source_layer_indexes = [
        12,
        14,
    ]

    '''
    SSD 구조에서 더 작은 크기의 feature map을 생성해 다양한 크기의 객체를 탐지할 수 있도록 돕는 역할,
    MobileNet이 끝난 후 추가되는 컨볼루션 레이어들이다.
    '''
    extras = ModuleList([
        Sequential(
            Conv2d(in_channels=1024, out_channels=256, kernel_size=1),
            ReLU(),
            Conv2d(in_channels=256, out_channels=512, kernel_size=3, stride=2, padding=1),
            ReLU()
        ),
        Sequential(
            Conv2d(in_channels=512, out_channels=128, kernel_size=1),
            ReLU(),
            Conv2d(in_channels=128, out_channels=256, kernel_size=3, stride=2, padding=1),
            ReLU()
        ),
        Sequential(
            Conv2d(in_channels=256, out_channels=128, kernel_size=1),
            ReLU(),
            Conv2d(in_channels=128, out_channels=256, kernel_size=3, stride=2, padding=1),
            ReLU()
        ),
        Sequential(
            Conv2d(in_channels=256, out_channels=128, kernel_size=1),
            ReLU(),
            Conv2d(in_channels=128, out_channels=256, kernel_size=3, stride=2, padding=1),
            ReLU()
        )
    ])

    '''
    feature map에 대해 물체의 바운딩 박스의 좌표를 예측을 위한 header들
    바운딩 박스(x, y, w, h)이걸 말함
    6 * 4는 각 픽셀에 대해 6개의 바운딩 박스를 예측하고, 각각의 바운딩 박스에 대해 4개의 값을 예측한다는 뜻
    6은 anchor box(기본 바운딩 박스)의 개수를 의미
    4는 각 anchor box에 대해 예측하는 값 4개, 즉 (x, y, w, h)를 나타냄

    GT와 최대한 일치하도록 학습하여 물체 탐지의 정확성을 높이는 역할
    '''
    regression_headers = ModuleList([
        Conv2d(in_channels=512, out_channels=6 * 4, kernel_size=3, padding=1),
        Conv2d(in_channels=1024, out_channels=6 * 4, kernel_size=3, padding=1),
        Conv2d(in_channels=512, out_channels=6 * 4, kernel_size=3, padding=1),
        Conv2d(in_channels=256, out_channels=6 * 4, kernel_size=3, padding=1),
        Conv2d(in_channels=256, out_channels=6 * 4, kernel_size=3, padding=1),
        Conv2d(in_channels=256, out_channels=6 * 4, kernel_size=3, padding=1), # TODO: change to kernel_size=1, padding=0?
    ])

    '''
    SSD 모델에서 물체의 클래스 확률을 예측
    6 * num_classes는 각 anchor box에 대해 각 클래스의 확률을 출력하도록 설정한 것이다.
    여기서 6은 바운딩 박스의 개수를 의미하며, num_classes는 탐지할 클래스의 수
    예측된 확률은 후속 단계에서 NMS(Non-Maximum Suppression)을 사용해 최종 탐지 결과로 이어짐
    '''
    classification_headers = ModuleList([
        Conv2d(in_channels=512, out_channels=6 * num_classes, kernel_size=3, padding=1),
        Conv2d(in_channels=1024, out_channels=6 * num_classes, kernel_size=3, padding=1),
        Conv2d(in_channels=512, out_channels=6 * num_classes, kernel_size=3, padding=1),
        Conv2d(in_channels=256, out_channels=6 * num_classes, kernel_size=3, padding=1),
        Conv2d(in_channels=256, out_channels=6 * num_classes, kernel_size=3, padding=1),
        Conv2d(in_channels=256, out_channels=6 * num_classes, kernel_size=3, padding=1), # TODO: change to kernel_size=1, padding=0?
    ])

    '''
    위 데이터를 사용하여 SSD를 함으로서 클래스 확률과 위치(바운딩 박스 좌표)를 반환
    '''
    return SSD(num_classes, base_net, source_layer_indexes,
               extras, classification_headers, regression_headers, is_test=is_test, config=config)
```

---
### <span style="color: rgb(0, 0, 255);">파이토치 SSD MobileNet-V1 학습</span>

```bash
# 파이토치 SSD MobileNetV1 학습
!python3 train_ssd.py --data=data/cctv --model-dir=models/cctv --batch-size=16 --epochs=5
```

```python
import cv2
import matplotlib.pyplot as plt
%matplotlib inline

#4. 이미지 보여주는 함수
def imShow(path):
  image = cv2.imread(path)
  height, width = image.shape[:2]
  resized_image = cv2.resize(image,(3*width, 3*height), interpolation = cv2.INTER_CUBIC)

  fig = plt.gcf()
  fig.set_size_inches(18, 10)
  plt.axis("off")
  plt.imshow(cv2.cvtColor(resized_image, cv2.COLOR_BGR2RGB))
  plt.show()
```

```bash
!ls -al models/cctv
```

```bash
!ls -al models/cctv/mb1-ssd-Epoch-4*
```

```bash
#6. 가장 오래 학습한 파일 파일을 mb1-ssd-cctv.pth 로 변경
# !mv models/cctv/mb1-ssd-Epoch-4-Loss-3.1566773561330943.pth models/cctv/mb1-ssd-cctv.pth
!mv models/cctv/mb1-ssd-Epoch-4-Loss-nan.pth models/cctv/mb1-ssd-cctv.pth
```

```bash
!ls models/cctv/mb1-ssd-cctv.pth
```

```python
# imShow('data/cctv/test/015df3fbcf17d6fe.jpg')

import matplotlib.pyplot as plt

# 이미지 파일 경로
image_path = 'data/cctv/test/00c8891d88ff36c1.jpg'

# 이미지 읽기
image = cv2.imread(image_path)

# OpenCV는 이미지를 BGR 포맷으로 읽기 때문에 RGB로 변환
image_rgb = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)
# 이미지 표시
plt.imshow(image_rgb)
plt.axis('off')  # 축을 표시하지 않음
plt.show()
```

```bash
!python3 run_ssd_example.py mb1-ssd models/cctv/mb1-ssd-cctv.pth models/cctv/labels.txt data/cctv/test/00c8891d88ff36c1.jpg
```

```python
imShow('run_ssd_example_output.jpg')
```

```python
imShow('data/cctv/test/394abd99234a0a50.jpg')
```

```bash
!python3 run_ssd_example.py mb1-ssd models/cctv/mb1-ssd-cctv.pth models/cctv/labels.txt data/cctv/test/394abd99234a0a50.jpg
```


```python
imShow('run_ssd_example_output.jpg')
```

# 동영상 예제

```bash
!python pytorch-ssd/inference_ssd_windows.py \
pytorch-ssd/models/ssd_example/ssd_cctv_sample.pth \
pytorch-ssd/models/ssd_example/labels.txt \
pytorch-ssd/data/run3.mp4
```

