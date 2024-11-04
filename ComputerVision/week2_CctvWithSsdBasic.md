
2주차
=====

MobileNet 요약
==================

MobileNet은 경량화된 딥러닝 모델로, 주로 모바일 기기나 임베디드 시스템에서 사용되는 모델입니다. Google에서 2017년에 발표한 모델로, 속도와 효율성을 중시하여 저성능 하드웨어에서도 신경망을 사용할 수 있게 설계되었습니다. MobileNet은 특히 이미지 분류(Classification), 객체 탐지(Object Detection), 세그멘테이션(Segmentation) 등의 컴퓨터 비전 작업에 많이 활용됩니다.

###    경량화된 구조 (Lightweight Architecture):
MobileNet은 복잡한 연산을 줄여, 저전력 기기에서 실시간으로 동작할 수 있도록 설계되었습니다. 특히, 연산량이 적고 메모리 사용량이 적어서 모바일 환경에서 효율적입니다.

###    Depthwise Separable Convolution:
MobileNet의 핵심 혁신 중 하나는 Depthwise Separable Convolution입니다. 일반적인 합성곱(Convolution)을 두 단계로 나누어 연산을 수행합니다:
    Depthwise Convolution: 각 채널에 대해 별도의 필터를 적용하는 과정. 즉, 하나의 필터가 한 채널만 처리합니다.
    Pointwise Convolution: 1x1 크기의 필터를 사용하여 각 채널을 결합하는 과정.
이 방식을 통해 연산량을 크게 줄일 수 있으며, 연산 속도가 빨라지고 모델이 가벼워집니다. 표준 합성곱(Convolution)의 계산량이 O(k^2 * D_in * D_out)이라면, Depthwise Separable Convolution의 계산량은 O(k^2 * D_in + D_in * D_out)으로 훨씬 적습니다.

###    Hyperparameter α (Width Multiplier):
MobileNet은 모델의 크기와 속도를 제어할 수 있는 하이퍼파라미터 **α(알파)**를 도입했습니다. α는 네트워크의 너비를 조정하여 모델을 더 가볍게 만들 수 있습니다.
    α = 1이면, 기본적인 MobileNet 구조를 사용하고, 1보다 작은 값을 주면 네트워크의 모든 레이어의 채널 수가 줄어들어 더 경량화된 모델이 됩니다.

###    Hyperparameter ρ (Resolution Multiplier):
입력 이미지의 해상도를 줄이는 **ρ(로우)**라는 하이퍼파라미터도 있습니다. ρ는 입력 이미지의 해상도를 낮추어 연산량을 줄입니다. 예를 들어, ρ = 0.5는 원래 이미지의 절반 크기를 사용하여 연산하는 것을 의미합니다. 이 방식으로 속도를 더욱 개선할 수 있습니다.

참고자료
------------

https://ctkim.tistory.com/entry/%EB%AA%A8%EB%B0%94%EC%9D%BC-%EB%84%B7

https://velog.io/@woojinn8/LightWeight-Deep-Learning-6.-MobileNet-2-MobileNet%EC%9D%98-%EA%B5%AC%EC%A1%B0-%EB%B0%8F-%EC%84%B1%EB%8A%A5

***

YOLO 와 SSD 의 특징과 차이점
===========================


YOLO(You Only Look Once)와 SSD(Single Shot Multibox Detector)는 둘 다 실시간 객체 탐지(Object Detection)를 위한 딥러닝 모델입니다. 이 두 모델은 비슷한 작업을 수행하지만, 그 방식과 세부적인 구조에서 차이가 있습니다. 아래에서 각 모델의 특징과 차이점을 설명하겠습니다.
YOLO (You Only Look Once)

특징:
------
End-to-End 처리: YOLO는 이미지를 한 번에 처리하여 물체의 위치(bounding box)와 클래스(class)를 동시에 예측합니다. 이미지에서 객체를 탐지할 때 이미지 전체를 한 번만 살펴보는 방식입니다.
속도: YOLO의 주요 장점은 매우 빠르다는 것입니다. 이미지의 전체를 한 번에 처리하므로 실시간 처리에 적합합니다.
전체 이미지 이해: YOLO는 이미지 전체를 보고 예측을 하기 때문에, 더 나은 전역 정보(global context)를 기반으로 한 예측이 가능합니다.
그리드 기반 탐지: 이미지를 그리드로 나누고 각 그리드 셀마다 객체가 있을 확률과 해당 객체의 바운딩 박스를 예측합니다.
버전: YOLO는 여러 버전이 존재합니다. 최신 버전으로 갈수록 성능과 정확도가 향상되며, YOLOv1, v2, v3, v4, 그리고 YOLOv5까지 발전해 왔습니다. 최근에는 YOLOv7, v8도 등장했습니다.

단점:
-----
작은 객체에 대한 탐지 성능이 상대적으로 떨어질 수 있습니다. 특히 이미지의 작은 부분에 있는 객체에 대해 정확한 탐지가 어려울 수 있습니다.
객체 간 겹침이나 복잡한 장면에서는 탐지 성능이 저하될 수 있습니다.

SSD (Single Shot Multibox Detector)
====================================
특징:
----
다중 크기에서 예측: SSD는 다양한 크기의 특징 맵(feature map)에서 바운딩 박스를 예측하여 작은 객체부터 큰 객체까지 다양한 크기의 객체를 탐지할 수 있습니다. 이를 통해 작은 객체에 대한 탐지 성능을 개선합니다.
멀티 스케일 피처 맵: 이미지에서 여러 해상도의 피처 맵을 생성하고, 각 해상도에서 객체를 탐지합니다. 이 방식은 다양한 크기의 객체에 대해 더 좋은 성능을 제공합니다.
빠른 속도: SSD는 속도가 매우 빠른 모델 중 하나로, 실시간 객체 탐지에 적합합니다.
단일 네트워크 구조: SSD는 YOLO와 마찬가지로 한 번의 네트워크 통과로 객체의 위치와 클래스를 예측하는 방식입니다.

단점:
----
SSD는 YOLO와 비교했을 때 상대적으로 속도는 빠르지만, YOLOv3 이후 버전과 비교하면 정확도에서 약간 부족할 수 있습니다.
다양한 크기의 객체를 탐지하는 데는 강점을 보이지만, 아주 복잡한 장면에서는 성능이 저하될 수 있습니다.

주요 차이점
-------------
그리드 방식 vs. 멀티 스케일 피처 맵:
YOLO는 이미지를 고정된 그리드로 나누고, 각 그리드에서 객체를 예측하는 방식입니다. 반면, SSD는 여러 크기의 특징 맵에서 객체를 탐지하기 때문에 작은 객체부터 큰 객체까지 다양한 크기의 물체를 더 잘 탐지할 수 있습니다.
속도와 정확도:
초기 YOLO(v1, v2)는 SSD보다 속도는 빠르지만 정확도는 낮은 경향이 있었습니다. 하지만 최신 YOLO(v3, v4, v5) 모델들은 정확도도 많이 개선되어 SSD와 비슷하거나 더 나은 성능을 보입니다.
적용 방식:
YOLO는 이미지 전체를 한 번에 처리하는 방식이라 전역적인 정보가 필요할 때 유리합니다. 반면 SSD는 여러 해상도의 특징 맵을 활용해 다양한 크기의 객체를 더 정밀하게 탐지할 수 있습니다.


***




SSD 사물인지 CCTV 프로젝트 소스코드 분석
==========================================

open_images.py
------------------
오픈 이미지 데이터세트를 파일에서 읽어와서 이미지와 라벨을 관리해 주는 클래스

```python

import numpy as np  # 숫자 배열 및 수치 계산을 위한 라이브러리
import pathlib  # 파일 경로를 관리하기 위한 라이브러리
import cv2  # OpenCV 라이브러리로 이미지 처리
import pandas as pd  # 데이터 프레임을 사용하여 CSV 데이터 처리
import copy  # 데이터 복사를 위한 라이브러리
import os  # 운영체제 파일 처리 라이브러리
import logging  # 로그를 기록하기 위한 라이브러리

# OpenImages 데이터셋을 처리하는 클래스
class OpenImagesDataset:

    # 클래스 초기화 메서드
    def __init__(self, root, transform=None, target_transform=None, dataset_type="train", balance_data=False):
        self.root = pathlib.Path(root)  # 데이터셋 루트 경로
        self.transform = transform  # 이미지 변환 함수
        self.target_transform = target_transform  # 타겟 변환 함수
        self.dataset_type = dataset_type.lower()  # 데이터셋 타입 (train, test, validation 등)

        # 데이터를 읽고 클래스 정보를 초기화
        self.data, self.class_names, self.class_dict = self._read_data()
        self.balance_data = balance_data  # 데이터 불균형 처리 여부
        self.min_image_num = -1  # 균형 맞추는 경우 최소 이미지 수를 설정
        if self.balance_data:  # 데이터 균형 맞추기 여부에 따라
            self.data = self._balance_data()  # 데이터 균형 맞추기
        self.ids = [info['image_id'] for info in self.data]  # 이미지 ID 목록

        self.class_stat = None  # 클래스 통계

    # 인덱스에 해당하는 항목 가져오기
    def _getitem(self, index):
        image_info = self.data[index]  # 이미지 정보
        image = self._read_image(image_info['image_id'])  # 이미지 파일 읽기

        # 바운딩 박스 복사 (데이터 손상 방지)
        boxes = copy.copy(image_info['boxes'])
        boxes[:, 0] *= image.shape[1]  # X 좌표 보정 (이미지 너비에 맞춤)
        boxes[:, 1] *= image.shape[0]  # Y 좌표 보정 (이미지 높이에 맞춤)
        boxes[:, 2] *= image.shape[1]  # 박스 너비 보정
        boxes[:, 3] *= image.shape[0]  # 박스 높이 보정

        # 레이블 복사 (데이터 손상 방지)
        labels = copy.copy(image_info['labels'])

        # 이미지 및 타겟 변환 적용
        if self.transform:
            image, boxes, labels = self.transform(image, boxes, labels)
        if self.target_transform:
            boxes, labels = self.target_transform(boxes, labels)

        return image_info['image_id'], image, boxes, labels

    # 클래스의 __getitem__ 메서드 (파이썬 내장 기능)
    def __getitem__(self, index):
        _, image, boxes, labels = self._getitem(index)
        return image, boxes, labels

    # 주석(annotation)을 가져오는 메서드
    def get_annotation(self, index):
        image_id, image, boxes, labels = self._getitem(index)
        is_difficult = np.zeros(boxes.shape[0], dtype=np.uint8)  # 'difficult' 속성 초기화
        return image_id, (boxes, labels, is_difficult)

    # 이미지 정보 가져오기
    def get_image(self, index):
        image_info = self.data[index]  # 이미지 정보 가져오기
        image = self._read_image(image_info['image_id'])  # 이미지 파일 읽기
        if self.transform:
            image, _ = self.transform(image)  # 이미지 변환 적용
        return image

    # 데이터를 읽어오는 메서드
    def _read_data(self):
        # 주석 파일 경로
        annotation_file = f"{self.root}/sub-{self.dataset_type}-annotations-bbox.csv"
        logging.info(f'loading annotations from: {annotation_file}')
        annotations = pd.read_csv(annotation_file)  # CSV 파일 읽기
        logging.info(f'annotations loaded from: {annotation_file}')

        # 클래스 이름 및 사전 초기화
        class_names = ['BACKGROUND'] + sorted(list(annotations['ClassName'].unique()))
        class_dict = {class_name: i for i, class_name in enumerate(class_names)}

        data = []  # 데이터를 담을 리스트

        # 이미지 ID별로 데이터를 그룹화
        for image_id, group in annotations.groupby("ImageID"):
            img_path = os.path.join(self.root, self.dataset_type, image_id + '.jpg')  # 이미지 경로
            if not os.path.isfile(img_path):
                logging.error(f'missing ImageID {image_id}.jpg - dropping from annotations')  # 파일이 없을 경우 로그 출력
                continue

            # 바운딩 박스 및 라벨 읽기
            boxes = group.loc[:, ["XMin", "YMin", "XMax", "YMax"]].values.astype(np.float32)
            labels = np.array([class_dict[name] for name in group["ClassName"]], dtype='int64')

            # 데이터 추가
            data.append({
                'image_id': image_id,
                'boxes': boxes,
                'labels': labels
            })

        print(f'num images: {len(data)}')  # 이미지 개수 출력
        return data, class_names, class_dict

    # 데이터셋의 크기를 반환하는 메서드
    def __len__(self):
        return len(self.data)

    # 데이터셋 요약 정보 출력 메서드
    def __repr__(self):
        if self.class_stat is None:
            self.class_stat = {name: 0 for name in self.class_names[1:]}  # 각 클래스의 통계 초기화
            for example in self.data:
                for class_index in example['labels']:  # 클래스 통계를 업데이트
                    class_name = self.class_names[class_index]
                    self.class_stat[class_name] += 1

        # 요약 정보를 문자열로 구성
        content = [
            "Dataset Summary:",
            f"Number of Images: {len(self.data)}",
            f"Minimum Number of Images for a Class: {self.min_image_num}",
            "Label Distribution:"
        ]

        for class_name, num in self.class_stat.items():
            content.append(f"\t{class_name}: {num}")

        return "\n".join(content)

    # 이미지를 읽는 메서드
    def _read_image(self, image_id):
        image_file = self.root / self.dataset_type / f"{image_id}.jpg"  # 이미지 파일 경로
        image = cv2.imread(str(image_file))  # 이미지 읽기
        if image.shape[2] == 1:  # 흑백 이미지일 경우
            image = cv2.cvtColor(image, cv2.COLOR_GRAY2RGB)  # 흑백을 RGB로 변환
        else:
            image = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)  # BGR을 RGB로 변환
        return image

    # 데이터 균형을 맞추는 메서드
    def _balance_data(self):
        logging.info('balancing data')  # 로그 기록

        # 클래스별로 이미지 인덱스 리스트 초기화
        label_image_indexes = [set() for _ in range(len(self.class_names))]

        # 각 이미지의 라벨에 따라 인덱스 저장
        for i, image in enumerate(self.data):
            for label_id in image['labels']:
                label_image_indexes[label_id].add(i)

        # 각 클래스별 이미지 수 계산
        label_stat = [len(s) for s in label_image_indexes]
        self.min_image_num = min(label_stat[1:])  # 최소 이미지 수 설정

        sample_image_indexes = set()  # 샘플 이미지 인덱스 저장할 집합
        for image_indexes in label_image_indexes[1:]:
            image_indexes = np.array(list(image_indexes))
            sub = np.random.permutation(image_indexes)[:self.min_image_num]  # 무작위 샘플링
            sample_image_indexes.update(sub)

        # 샘플 데이터를 새로 설정
        sample_data = [self.data[i] for i in sample_image_indexes]
        return sample_data

```


mobilenet.py
----------------------------
SSD MobileNet v1 을 구현하는데 필요한 CNN 백본

```python
# 필요한 파이토치 라이브러리 임포트
import torch.nn as nn  # 신경망 구성 관련 모듈
import torch.nn.functional as F  # 활성화 함수 등 기본적인 연산을 위한 모듈

# MobileNetV1 클래스 정의 (기본적으로 이미지 분류를 위한 신경망)
class MobileNetV1(nn.Module):
    # 클래스 초기화 메서드
    def __init__(self, num_classes=1024):  # 분류할 클래스의 개수를 기본적으로 1024개로 설정
        super(MobileNetV1, self).__init__()

        # Convolution + BatchNorm + ReLU 레이어 정의
        # inp: 입력 채널, oup: 출력 채널, stride: 스트라이드 크기
        def conv_bn(inp, oup, stride):
            return nn.Sequential(
                nn.Conv2d(inp, oup, 3, stride, 1, bias=False),  # 3x3 Conv2D 레이어
                nn.BatchNorm2d(oup),  # Batch Normalization
                nn.ReLU(inplace=True)  # ReLU 활성화 함수
            )

        # Depthwise Convolution + Pointwise Convolution 정의
        def conv_dw(inp, oup, stride):
            return nn.Sequential(
                # Depthwise Convolution: 각 입력 채널에 대해 별도의 필터 적용
                nn.Conv2d(inp, inp, 3, stride, 1, groups=inp, bias=False),
                nn.BatchNorm2d(inp),
                nn.ReLU(inplace=True),

                # Pointwise Convolution: 1x1 Conv2D 레이어로 입력 채널 수를 줄임
                nn.Conv2d(inp, oup, 1, 1, 0, bias=False),
                nn.BatchNorm2d(oup),
                nn.ReLU(inplace=True),
            )

        # MobileNet의 전체 모델 정의
        # pooling 을 전혀 사용하지 않는 대신  stride를 2로 줘서 사이즈를 줄임(다운샘플링)
        self.model = nn.Sequential(
            conv_bn(3, 32, 2),  # 처음에 입력 채널 3개 (RGB 이미지) -> 출력 32채널, 스트라이드 2
            conv_dw(32, 64, 1),  # Depthwise 및 Pointwise Conv (32채널 -> 64채널)
            conv_dw(64, 128, 2),  # 스트라이드 2로 다운샘플링
            conv_dw(128, 128, 1),
            conv_dw(128, 256, 2),
            conv_dw(256, 256, 1),
            conv_dw(256, 512, 2),
            conv_dw(512, 512, 1),  # 512 채널을 유지하며 여러 번 연산
            conv_dw(512, 512, 1),
            conv_dw(512, 512, 1),
            conv_dw(512, 512, 1),
            conv_dw(512, 512, 1),
            conv_dw(512, 1024, 2),  # 최종적으로 1024채널로 변환
            conv_dw(1024, 1024, 1),
        )
        self.fc = nn.Linear(1024, num_classes)  # 최종적으로 클래스 수에 맞게 Fully Connected Layer

    # Forward 메서드: 입력 데이터를 받아 모델을 통과시킨 후 결과를 반환
    def forward(self, x):
        x = self.model(x)  # CNN 모델 통과
        x = F.avg_pool2d(x, 7)  # 평균 풀링 (출력 크기를 축소)
        x = x.view(-1, 1024)  # 1차원으로 변환
        x = self.fc(x)  # Fully Connected Layer 통과
        return x  # 최종 출력 반환

```

ssd.py
----------------------------
SSD 객체인식을 하기 위한 SSD 클래스 코드

```python
# 필요한 파이토치 및 기타 라이브러리 임포트
import torch.nn as nn  # 신경망 관련 모듈
import torch  # 파이토치의 핵심 모듈
import numpy as np  # 수학 연산 관련 모듈
from typing import List, Tuple  # 타입 힌팅을 위한 모듈
import torch.nn.functional as F  # 신경망에서 자주 사용하는 함수들

from ..utils import box_utils  # 박스 유틸리티 함수 임포트
from collections import namedtuple  # 네임드 튜플을 위한 모듈
GraphPath = namedtuple("GraphPath", ['s0', 'name', 's1'])  # 네임드 튜플 정의

# SSD (Single Shot MultiBox Detector) 모델 클래스 정의
class SSD(nn.Module):
    # 초기화 메서드
    def __init__(self, num_classes: int, base_net: nn.ModuleList, source_layer_indexes: List[int],
                 extras: nn.ModuleList, classification_headers: nn.ModuleList,
                 regression_headers: nn.ModuleList, is_test=False, config=None, device=None):
        """SSD 모델을 주어진 컴포넌트들로 구성합니다."""
        super(SSD, self).__init__()

        self.num_classes = num_classes  # 분류할 클래스 수
        self.base_net = base_net  # 기본 네트워크 (기본적인 피처 추출 레이어)
        self.source_layer_indexes = source_layer_indexes  # 피처 추출을 위한 레이어 인덱스
        self.extras = extras  # 추가 레이어
        self.classification_headers = classification_headers  # 분류 헤더
        self.regression_headers = regression_headers  # 회귀 헤더 (박스 위치 예측)
        self.is_test = is_test  # 테스트 여부
        self.config = config  # 설정 값

        # source_layer_indexes에서 레이어 추가를 위한 nn.ModuleList
        self.source_layer_add_ons = nn.ModuleList([t[1] for t in source_layer_indexes
                                                   if isinstance(t, tuple) and not isinstance(t, GraphPath)])
        # 장치 설정
        if device:
            self.device = device
        else:
            self.device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
        # 테스트 모드일 경우, priors(기본 앵커 박스) 설정
        if is_test:
            self.config = config
            self.priors = config.priors.to(self.device)
    
    # 모델 순전파(forward) 메서드 정의
    def forward(self, x: torch.Tensor) -> Tuple[torch.Tensor, torch.Tensor]:
        confidences = []  # 각 클래스에 대한 신뢰도 저장
        locations = []  # 박스 위치 저장
        start_layer_index = 0
        header_index = 0

        # source_layer_indexes의 인덱스까지 기본 네트워크를 순차적으로 통과
        for end_layer_index in self.source_layer_indexes:
            if isinstance(end_layer_index, GraphPath):  # GraphPath가 주어졌을 때
                path = end_layer_index
                end_layer_index = end_layer_index.s0
                added_layer = None
            elif isinstance(end_layer_index, tuple):  # 튜플이 주어졌을 때
                added_layer = end_layer_index[1]
                end_layer_index = end_layer_index[0]
                path = None
            else:
                added_layer = None
                path = None

            # base_net의 레이어들을 순차적으로 통과
            for layer in self.base_net[start_layer_index: end_layer_index]:
                x = layer(x)
            
            if added_layer:  # 추가된 레이어가 있는 경우
                y = added_layer(x)
            else:
                y = x

            if path:  # GraphPath가 있을 경우 추가 연산
                sub = getattr(self.base_net[end_layer_index], path.name)
                for layer in sub[:path.s1]:
                    x = layer(x)
                y = x
                for layer in sub[path.s1:]:
                    x = layer(x)
                end_layer_index += 1

            start_layer_index = end_layer_index
            # 분류 및 회귀 헤더에서 결과 계산
            confidence, location = self.compute_header(header_index, y)
            header_index += 1
            confidences.append(confidence)
            locations.append(location)

        # 나머지 base_net 레이어들을 순차적으로 통과
        for layer in self.base_net[end_layer_index:]:
            x = layer(x)

        # extras 레이어를 통과하며 추가 연산
        for layer in self.extras:
            x = layer(x)
            confidence, location = self.compute_header(header_index, x)
            header_index += 1
            confidences.append(confidence)
            locations.append(location)

        # 최종 결과를 연결
        confidences = torch.cat(confidences, 1)
        locations = torch.cat(locations, 1)

        # 테스트 모드일 경우, 결과에 소프트맥스를 적용하고 박스 좌표를 변환
        if self.is_test:
            confidences = F.softmax(confidences, dim=2)
            boxes = box_utils.convert_locations_to_boxes(
                locations, self.priors, self.config.center_variance, self.config.size_variance
            )
            boxes = box_utils.center_form_to_corner_form(boxes)
            return confidences, boxes
        else:
            return confidences, locations

    # 분류와 회귀 헤더 계산
    def compute_header(self, i, x):
        # 분류 헤더 계산
        confidence = self.classification_headers[i](x)
        confidence = confidence.permute(0, 2, 3, 1).contiguous()
        confidence = confidence.view(confidence.size(0), -1, self.num_classes)

        # 회귀 헤더 계산 (박스 위치 예측)
        location = self.regression_headers[i](x)
        location = location.permute(0, 2, 3, 1).contiguous()
        location = location.view(location.size(0), -1, 4)

        return confidence, location

    # 기본 네트워크로부터 초기화
    def init_from_base_net(self, model):
        self.base_net.load_state_dict(torch.load(model, map_location=lambda storage, loc: storage), strict=True)
        self.source_layer_add_ons.apply(_xavier_init_)
        self.extras.apply(_xavier_init_)
        self.classification_headers.apply(_xavier_init_)
        self.regression_headers.apply(_xavier_init_)

    # 사전 학습된 SSD로부터 초기화
    def init_from_pretrained_ssd(self, model):
        state_dict = torch.load(model, map_location=lambda storage, loc: storage)
        state_dict = {k: v for k, v in state_dict.items() if not (k.startswith("classification_headers") or k.startswith("regression_headers"))}
        model_dict = self.state_dict()
        model_dict.update(state_dict)
        self.load_state_dict(model_dict)
        self.classification_headers.apply(_xavier_init_)
        self.regression_headers.apply(_xavier_init_)

    # 모델 전체 초기화
    def init(self):
        self.base_net.apply(_xavier_init_)
        self.source_layer_add_ons.apply(_xavier_init_)
        self.extras.apply(_xavier_init_)
        self.classification_headers.apply(_xavier_init_)
        self.regression_headers.apply(_xavier_init_)

    # 모델 로드
    def load(self, model):
        self.load_state_dict(torch.load(model, map_location=lambda storage, loc: storage))

    # 모델 저장
    def save(self, model_path):
        torch.save(self.state_dict(), model_path)


# 박스 매칭 및 손실 계산을 위한 클래스
class MatchPrior(object):
    def __init__(self, center_form_priors, center_variance, size_variance, iou_threshold):
        self.center_form_priors = center_form_priors
        self.corner_form_priors = box_utils.center_form_to_corner_form(center_form_priors)
        self.center_variance = center_variance
        self.size_variance = size_variance
        self.iou_threshold = iou_threshold

    # 박스 및 레이블을 매칭
    def __call__(self, gt_boxes, gt_labels):
        if type(gt_boxes) is np.ndarray:
            gt_boxes = torch.from_numpy(gt_boxes)
        if type(gt_labels) is np.ndarray:
            gt_labels = torch.from_numpy(gt_labels)
        boxes, labels = box_utils.assign_priors(gt_boxes, gt_labels,
                                                self.corner_form_priors, self.iou_threshold)
        boxes = box_utils.corner_form_to_center_form(boxes)
        locations = box_utils.convert_boxes_to_locations(boxes, self.center_form_priors, self.center_variance, self.size_variance)
        return locations, labels


# Xavier 초기화 함수
def _xavier_init_(m: nn.Module):
    if isinstance(m, nn.Conv2d):
        nn.init.xavier_uniform_(m.weight)

```

mobilenetv1_ssd.py
----------------------------
SSD MobileNet v1 네트워크 생성함수

```python
import torch
from torch.nn import Conv2d, Sequential, ModuleList, ReLU
from ..nn.mobilenet import MobileNetV1  # MobileNetV1 모델을 임포트합니다.

from .ssd import SSD  # SSD 모델을 임포트합니다.
from .predictor import Predictor  # 예측기(Predictor)를 임포트합니다.
from .config import mobilenetv1_ssd_config as config  # 설정(config)을 임포트합니다.


def create_mobilenetv1_ssd(num_classes, is_test=False):
    base_net = MobileNetV1(1001).model  # MobileNetV1 모델을 생성합니다. dropout 레이어는 비활성화됩니다.

    # 소스 레이어 인덱스를 설정합니다.
    source_layer_indexes = [
        12,  # 첫 번째 소스 레이어 인덱스
        14,  # 두 번째 소스 레이어 인덱스
    ]
    
    # 추가 레이어(extra layers)를 정의합니다.
    extras = ModuleList([
        Sequential(
            Conv2d(in_channels=1024, out_channels=256, kernel_size=1),  # 채널 수를 줄이는 1x1 합성곱 레이어
            ReLU(),  # 활성화 함수
            Conv2d(in_channels=256, out_channels=512, kernel_size=3, stride=2, padding=1),  # 3x3 합성곱 레이어
            ReLU()  # 활성화 함수
        ),
        Sequential(
            Conv2d(in_channels=512, out_channels=128, kernel_size=1),  # 채널 수를 줄이는 1x1 합성곱 레이어
            ReLU(),  # 활성화 함수
            Conv2d(in_channels=128, out_channels=256, kernel_size=3, stride=2, padding=1),  # 3x3 합성곱 레이어
            ReLU()  # 활성화 함수
        ),
        Sequential(
            Conv2d(in_channels=256, out_channels=128, kernel_size=1),  # 채널 수를 줄이는 1x1 합성곱 레이어
            ReLU(),  # 활성화 함수
            Conv2d(in_channels=128, out_channels=256, kernel_size=3, stride=2, padding=1),  # 3x3 합성곱 레이어
            ReLU()  # 활성화 함수
        ),
        Sequential(
            Conv2d(in_channels=256, out_channels=128, kernel_size=1),  # 채널 수를 줄이는 1x1 합성곱 레이어
            ReLU(),  # 활성화 함수
            Conv2d(in_channels=128, out_channels=256, kernel_size=3, stride=2, padding=1),  # 3x3 합성곱 레이어
            ReLU()  # 활성화 함수
        )
    ])

    # 회귀 헤더(regression headers)를 정의합니다.
    regression_headers = ModuleList([
        Conv2d(in_channels=512, out_channels=6 * 4, kernel_size=3, padding=1),  # 회귀를 위한 3x3 합성곱 레이어
        Conv2d(in_channels=1024, out_channels=6 * 4, kernel_size=3, padding=1),  # 회귀를 위한 3x3 합성곱 레이어
        Conv2d(in_channels=512, out_channels=6 * 4, kernel_size=3, padding=1),  # 회귀를 위한 3x3 합성곱 레이어
        Conv2d(in_channels=256, out_channels=6 * 4, kernel_size=3, padding=1),  # 회귀를 위한 3x3 합성곱 레이어
        Conv2d(in_channels=256, out_channels=6 * 4, kernel_size=3, padding=1),  # 회귀를 위한 3x3 합성곱 레이어
        Conv2d(in_channels=256, out_channels=6 * 4, kernel_size=3, padding=1),  # 회귀를 위한 3x3 합성곱 레이어
    ])

    # 분류 헤더(classification headers)를 정의합니다.
    classification_headers = ModuleList([
        Conv2d(in_channels=512, out_channels=6 * num_classes, kernel_size=3, padding=1),  # 분류를 위한 3x3 합성곱 레이어
        Conv2d(in_channels=1024, out_channels=6 * num_classes, kernel_size=3, padding=1),  # 분류를 위한 3x3 합성곱 레이어
        Conv2d(in_channels=512, out_channels=6 * num_classes, kernel_size=3, padding=1),  # 분류를 위한 3x3 합성곱 레이어
        Conv2d(in_channels=256, out_channels=6 * num_classes, kernel_size=3, padding=1),  # 분류를 위한 3x3 합성곱 레이어
        Conv2d(in_channels=256, out_channels=6 * num_classes, kernel_size=3, padding=1),  # 분류를 위한 3x3 합성곱 레이어
        Conv2d(in_channels=256, out_channels=6 * num_classes, kernel_size=3, padding=1),  # 분류를 위한 3x3 합성곱 레이어
    ])

    # SSD 모델을 반환합니다.
    return SSD(num_classes, base_net, source_layer_indexes,
               extras, classification_headers, regression_headers, is_test=is_test, config=config)


def create_mobilenetv1_ssd_predictor(net, candidate_size=200, nms_method=None, sigma=0.5, device=None):
    # Predictor 객체를 생성하여 반환합니다.
    predictor = Predictor(net, config.image_size, config.image_mean,
                          config.image_std,
                          nms_method=nms_method,
                          iou_threshold=config.iou_threshold,
                          candidate_size=candidate_size,
                          sigma=sigma,
                          device=device)
    return predictor

```

## 파이토치 SSD MobileNetV1 학습
*다음 명령 실행시 에러발생하므로 iteritems() -> items() 로 코드수정해야 합니다.*
```
!python3 train_ssd.py --data=data/cctv --model-dir=models/cctv --batch-size=16 --epochs=3
```

train_ssd.py
------------
구글의 오픈 이미지 데이터세트로 모델을 학습시킬 수 있는 파이썬코드
여기에서 cctv사물인지를 위해 내려받은 데이터셋트를 SSD MobileNet v1 네트워크로 학습시키는 함수를 확인할 수 있음

```python

#
# Open Image dataset 으로 SSD 모델을 학습시킬 수 있는 파이썬 코드
#

#1. 시스템, 데이터로더, 스케줄러, 토치 필요한 패키지들 임포트
import os
import sys
import logging
import argparse
import itertools
import torch

from torch.utils.data import DataLoader, ConcatDataset
from torch.optim.lr_scheduler import CosineAnnealingLR, MultiStepLR

#2. 하위 디렉토리 vision에 있는 ssd, dataset, config 등의 패키지 임포트
from vision.utils.misc import str2bool, Timer, freeze_net_layers, store_labels
from vision.ssd.ssd import MatchPrior
from vision.ssd.vgg_ssd import create_vgg_ssd
from vision.ssd.mobilenetv1_ssd import create_mobilenetv1_ssd
from vision.ssd.mobilenetv1_ssd_lite import create_mobilenetv1_ssd_lite
from vision.ssd.mobilenet_v2_ssd_lite import create_mobilenetv2_ssd_lite
from vision.ssd.squeezenet_ssd_lite import create_squeezenet_ssd_lite
from vision.datasets.voc_dataset import VOCDataset
from vision.datasets.open_images import OpenImagesDataset
from vision.nn.multibox_loss import MultiboxLoss
from vision.ssd.config import vgg_ssd_config
from vision.ssd.config import mobilenetv1_ssd_config
from vision.ssd.config import squeezenet_ssd_config
from vision.ssd.data_preprocessing import TrainAugmentation, TestTransform

#3. 실행 인자 파서로 데이터셋, 네트워크, 미리 훈련된 모델, SGD 등의 변수 세팅
parser = argparse.ArgumentParser(
    description='Single Shot MultiBox Detector Training With PyTorch')

# 데이터셋 인자
parser.add_argument("--dataset-type", default="open_images", type=str,
                    help='Specify dataset type. Currently supports voc and open_images.')
parser.add_argument('--datasets', '--data', nargs='+', default=["data"], help='Dataset directory path')
parser.add_argument('--balance-data', action='store_true',
                    help="Balance training data by down-sampling more frequent labels.")

# 네트워크 인자
parser.add_argument('--net', default="mb1-ssd",
                    help="The network architecture, it can be mb1-ssd, mb1-lite-ssd, mb2-ssd-lite or vgg16-ssd.")
parser.add_argument('--freeze-base-net', action='store_true',
                    help="Freeze base net layers.")
parser.add_argument('--freeze-net', action='store_true',
                    help="Freeze all the layers except the prediction head.")
parser.add_argument('--mb2-width-mult', default=1.0, type=float,
                    help='Width Multiplifier for MobilenetV2')

# 미리 훈련된 모델 인자.
parser.add_argument('--base-net', help='Pretrained base model')
parser.add_argument('--pretrained-ssd', default='models/mobilenet-v1-ssd-mp-0_675.pth', type=str, help='Pre-trained base model')
parser.add_argument('--resume', default=None, type=str,
                    help='Checkpoint state_dict file to resume training from')

# SGD 인자
parser.add_argument('--lr', '--learning-rate', default=0.01, type=float,
                    help='initial learning rate')
parser.add_argument('--momentum', default=0.9, type=float,
                    help='Momentum value for optim')
parser.add_argument('--weight-decay', default=5e-4, type=float,
                    help='Weight decay for SGD')
parser.add_argument('--gamma', default=0.1, type=float,
                    help='Gamma update for SGD')
parser.add_argument('--base-net-lr', default=0.001, type=float,
                    help='initial learning rate for base net, or None to use --lr')
parser.add_argument('--extra-layers-lr', default=None, type=float,
                    help='initial learning rate for the layers not in base net and prediction heads.')

# Scheduler
parser.add_argument('--scheduler', default="cosine", type=str,
                    help="Scheduler for SGD. It can one of multi-step and cosine")
# Params for Multi-step Scheduler
parser.add_argument('--milestones', default="80,100", type=str,
                    help="milestones for MultiStepLR")

# Params for Cosine Annealing
parser.add_argument('--t-max', default=100, type=float,
                    help='T_max value for Cosine Annealing Scheduler.')


# 학습에 관련된 배치 사이즈, 에포크 수 등의 인자
parser.add_argument('--batch-size', default=4, type=int,
                    help='Batch size for training')
parser.add_argument('--num-epochs', '--epochs', default=30, type=int,
                    help='the number epochs')
parser.add_argument('--num-workers', '--workers', default=2, type=int,
                    help='Number of workers used in dataloading')
parser.add_argument('--validation-epochs', default=1, type=int,
                    help='the number epochs between running validation')
parser.add_argument('--debug-steps', default=10, type=int,
                    help='Set the debug log output frequency.')
parser.add_argument('--use-cuda', default=True, type=str2bool,
                    help='Use CUDA to train model')
parser.add_argument('--checkpoint-folder', '--model-dir', default='models/',
                    help='Directory for saving checkpoint models')

logging.basicConfig(stream=sys.stdout, level=logging.INFO,
                    format='%(asctime)s - %(message)s', datefmt="%Y-%m-%d %H:%M:%S")
    
#4. 인자 변수, 쿠다 사용 처리    
args = parser.parse_args()
DEVICE = torch.device("cuda:0" if torch.cuda.is_available() and args.use_cuda else "cpu")

if args.use_cuda and torch.cuda.is_available():
    torch.backends.cudnn.benchmark = True
    logging.info("Using CUDA...")

#5. 훈련 함수
def train(loader, net, criterion, optimizer, device, debug_steps=100, epoch=-1):
    net.train(True)
    running_loss = 0.0
    running_regression_loss = 0.0
    running_classification_loss = 0.0
    for i, data in enumerate(loader):
        images, boxes, labels = data
        images = images.to(device)
        boxes = boxes.to(device)
        labels = labels.to(device)

        optimizer.zero_grad()
        # 훈련 데이터 네트워크 모델 계산
        confidence, locations = net(images)
        # 훈련 데이터 손실값 계산, 역전파, 최적화
        regression_loss, classification_loss = criterion(confidence, locations, labels, boxes) 
        loss = regression_loss + classification_loss
        loss.backward()
        optimizer.step()

        running_loss += loss.item()
        running_regression_loss += regression_loss.item()
        running_classification_loss += classification_loss.item()
        if i and i % debug_steps == 0:
            avg_loss = running_loss / debug_steps
            avg_reg_loss = running_regression_loss / debug_steps
            avg_clf_loss = running_classification_loss / debug_steps
            running_loss = 0.0
            running_regression_loss = 0.0
            running_classification_loss = 0.0
    logging.info(
        f"TRIAIN Epoch: {epoch},  " +
        f"Avg Loss: {avg_loss:.4f}, " +
        f"Avg Regression Loss {avg_reg_loss:.4f}, " +
        f"Avg Classification Loss: {avg_clf_loss:.4f}"
    )

#6. 테스트 함수    
def test(loader, net, criterion, device):
    net.eval()
    running_loss = 0.0
    running_regression_loss = 0.0
    running_classification_loss = 0.0
    num = 0
    for _, data in enumerate(loader):
        images, boxes, labels = data
        images = images.to(device)
        boxes = boxes.to(device)
        labels = labels.to(device)
        num += 1

        with torch.no_grad():
            # 테스트 데이터 네트워크 모델 계산
            confidence, locations = net(images)
            # 테스트 데이터 손실값 계산
            regression_loss, classification_loss = criterion(confidence, locations, labels, boxes)
            loss = regression_loss + classification_loss

        running_loss += loss.item()
        running_regression_loss += regression_loss.item()
        running_classification_loss += classification_loss.item()
    return running_loss / num, running_regression_loss / num, running_classification_loss / num

#7. 메인 함수 시작    
if __name__ == '__main__':
    timer = Timer()

    logging.info(args)
    
    # 8. 체크포인트 폴더 (모델 폴더) 확인
    if args.checkpoint_folder:
        args.checkpoint_folder = os.path.expanduser(args.checkpoint_folder)

        if not os.path.exists(args.checkpoint_folder):
            os.mkdir(args.checkpoint_folder)
            
    # 9. 네트워크 지정     
    if args.net == 'vgg16-ssd':
        create_net = create_vgg_ssd
        config = vgg_ssd_config
    elif args.net == 'mb1-ssd':
        create_net = create_mobilenetv1_ssd
        config = mobilenetv1_ssd_config
    elif args.net == 'mb1-ssd-lite':
        create_net = create_mobilenetv1_ssd_lite
        config = mobilenetv1_ssd_config
    elif args.net == 'sq-ssd-lite':
        create_net = create_squeezenet_ssd_lite
        config = squeezenet_ssd_config
    elif args.net == 'mb2-ssd-lite':
        create_net = lambda num: create_mobilenetv2_ssd_lite(num, width_mult=args.mb2_width_mult)
        config = mobilenetv1_ssd_config
    else:
        logging.fatal("The net type is wrong.")
        parser.print_help(sys.stderr)
        sys.exit(1)
        
    # 10. 훈련 데이터, 테스트 데이터 전처리 준비
    train_transform = TrainAugmentation(config.image_size, config.image_mean, config.image_std)
    target_transform = MatchPrior(config.priors, config.center_variance,
                                  config.size_variance, 0.5)

    test_transform = TestTransform(config.image_size, config.image_mean, config.image_std)

    # 11. 데이터셋 로딩 
    logging.info("Prepare training datasets.")
    datasets = []
    for dataset_path in args.datasets:
        dataset = OpenImagesDataset(dataset_path,
              transform=train_transform, target_transform=target_transform,
              dataset_type="train", balance_data=args.balance_data)
        label_file = os.path.join(args.checkpoint_folder, "labels.txt")
        store_labels(label_file, dataset.class_names)
        logging.info(dataset)
        num_classes = len(dataset.class_names)
        datasets.append(dataset)
        
    # 12. 훈련 데이터셋 만들기
    logging.info(f"Stored labels into file {label_file}.")
    train_dataset = ConcatDataset(datasets)
    logging.info("Train dataset size: {}".format(len(train_dataset)))
    train_loader = DataLoader(train_dataset, args.batch_size,
                              num_workers=args.num_workers,
                              shuffle=True)
                           
    # 13. 검증 데이터셋 만들기                 
    logging.info("Prepare Validation datasets.")
    if args.dataset_type == "voc": #PASCAL VOC 데이터셋을 사용하여 검증 데이터셋을 생성합니다.
        val_dataset = VOCDataset(dataset_path, transform=test_transform,
                                 target_transform=target_transform, is_test=True)
    elif args.dataset_type == 'open_images':
        val_dataset = OpenImagesDataset(dataset_path,
                                        transform=test_transform, target_transform=target_transform,
                                        dataset_type="test")
        logging.info(val_dataset)
    logging.info("Validation dataset size: {}".format(len(val_dataset)))

    val_loader = DataLoader(val_dataset, args.batch_size,
                            num_workers=args.num_workers,
                            shuffle=False)
                            
    # 14. 네트워크 객체 생성
    logging.info("Build network.")
    net = create_net(num_classes)
    min_loss = -10000.0
    last_epoch = -1

    base_net_lr = args.base_net_lr if args.base_net_lr is not None else args.lr
    extra_layers_lr = args.extra_layers_lr if args.extra_layers_lr is not None else args.lr

    params = [
      {'params': net.base_net.parameters(), 'lr': base_net_lr},
      {'params': itertools.chain(
                net.source_layer_add_ons.parameters(),
                net.extras.parameters()
             ), 'lr': extra_layers_lr},
      {'params': itertools.chain(
                net.regression_headers.parameters(),
                net.classification_headers.parameters()
              )
      }
    ] 

    # 15. 미리 훈련된 모델이 있는 경우 처리
    timer.start("Load Model")
    if args.resume:
        logging.info(f"Resume from the model {args.resume}")
        net.load(args.resume)
    elif args.base_net:
        logging.info(f"Init from base net {args.base_net}")
        net.init_from_base_net(args.base_net)
    elif args.pretrained_ssd:
        logging.info(f"Init from pretrained ssd {args.pretrained_ssd}")
        net.init_from_pretrained_ssd(args.pretrained_ssd)
    logging.info(f'Took {timer.end("Load Model"):.2f} seconds to load the model.')

    # 16. GPU에서 훈련하도록 지정
    net.to(DEVICE)

    # 17. 손실함수와 최적화 처리
    criterion = MultiboxLoss(config.priors, iou_threshold=0.5, neg_pos_ratio=3,
                             center_variance=0.1, size_variance=0.2, device=DEVICE)
    optimizer = torch.optim.SGD(params, lr=args.lr, momentum=args.momentum,
                                weight_decay=args.weight_decay)
    logging.info(f"Learning rate: {args.lr}, Base net learning rate: {base_net_lr}, "
                 + f"Extra Layers learning rate: {extra_layers_lr}.")

    # 18. 학습률과 학습률 감소 정책 지정
    if args.scheduler == 'multi-step':
        logging.info("Uses MultiStepLR scheduler.")
        milestones = [int(v.strip()) for v in args.milestones.split(",")]
        scheduler = MultiStepLR(optimizer, milestones=milestones,
                                                     gamma=0.1, last_epoch=last_epoch)
    elif args.scheduler == 'cosine':
        logging.info("Uses CosineAnnealingLR scheduler.")
        scheduler = CosineAnnealingLR(optimizer, args.t_max, last_epoch=last_epoch)
    else:
        logging.fatal(f"Unsupported Scheduler: {args.scheduler}.")
        parser.print_help(sys.stderr)
        sys.exit(1)

    # 19. 지정한 에포크 수 만큼 훈련
    logging.info(f"Start training from epoch {last_epoch + 1}.")
    
    for epoch in range(last_epoch + 1, args.num_epochs):
        scheduler.step()
        train(train_loader, net, criterion, optimizer,
              device=DEVICE, debug_steps=args.debug_steps, epoch=epoch)
        
        if epoch % args.validation_epochs == 0 or epoch == args.num_epochs - 1:
            val_loss, val_regression_loss, val_classification_loss = test(val_loader, net, criterion, DEVICE)
            logging.info(
                f"Validation Epoch: {epoch}, " +
                f"Validation Loss: {val_loss:.4f}, " +
                f"Validation Regression Loss {val_regression_loss:.4f}, " +
                f"Validation Classification Loss: {val_classification_loss:.4f}"
            )
            model_path = os.path.join(args.checkpoint_folder, f"{args.net}-Epoch-{epoch}-Loss-{val_loss}.pth")
            net.save(model_path)
            logging.info(f"Saved model {model_path}")

    logging.info("Task done, exiting program.")
```



run_ssd_example.py
-----------------------------
사진 파일 추론시 사용합니다.

```python

# SSD 모델 추론 파이썬 코드

# 1. 시스템, opencv, 그리고 하위 디렉토리 vision에 있는 ssd 패키지 임포트
from vision.ssd.vgg_ssd import create_vgg_ssd, create_vgg_ssd_predictor
from vision.ssd.mobilenetv1_ssd import create_mobilenetv1_ssd, create_mobilenetv1_ssd_predictor
from vision.ssd.mobilenetv1_ssd_lite import create_mobilenetv1_ssd_lite, create_mobilenetv1_ssd_lite_predictor
from vision.ssd.squeezenet_ssd_lite import create_squeezenet_ssd_lite, create_squeezenet_ssd_lite_predictor
from vision.ssd.mobilenet_v2_ssd_lite import create_mobilenetv2_ssd_lite, create_mobilenetv2_ssd_lite_predictor
from vision.utils.misc import Timer
import cv2
import sys

# 2. 실행 인자 파서로 네트워크, 모델 폴더, 라벨 폴더, 이미지 폴더 세팅
if len(sys.argv) < 5:
    print('Usage: python run_ssd_example.py <net type>  <model path> <label path> <image path>')
    sys.exit(0)

# 커맨드라인 인자에서 네트워크 타입, 모델 경로, 라벨 경로, 이미지 경로를 가져옵니다.
net_type = sys.argv[1]  # 네트워크 타입 (예: vgg16-ssd, mb1-ssd 등)
model_path = sys.argv[2]  # 모델 파일 경로
label_path = sys.argv[3]  # 라벨 파일 경로
image_path = sys.argv[4]  # 이미지 파일 경로

# 3. 라벨 파일을 읽어 클래스 이름들 세팅
class_names = [name.strip() for name in open(label_path).readlines()]
# 라벨 파일을 읽어 각 클래스의 이름을 리스트로 저장합니다.

# 4. 모델 파일을 읽어 모델 로딩, 각 네트워크의 인스턴스 생성
if net_type == 'vgg16-ssd':
    net = create_vgg_ssd(len(class_names), is_test=True)
    # VGG16 기반 SSD 모델을 생성합니다.
elif net_type == 'mb1-ssd':
    net = create_mobilenetv1_ssd(len(class_names), is_test=True)
    # MobileNetV1 기반 SSD 모델을 생성합니다.
elif net_type == 'mb1-ssd-lite':
    net = create_mobilenetv1_ssd_lite(len(class_names), is_test=True)
    # MobileNetV1-Lite 기반 SSD 모델을 생성합니다.
elif net_type == 'mb2-ssd-lite':
    net = create_mobilenetv2_ssd_lite(len(class_names), is_test=True)
    # MobileNetV2-Lite 기반 SSD 모델을 생성합니다.
elif net_type == 'sq-ssd-lite':
    net = create_squeezenet_ssd_lite(len(class_names), is_test=True)
    # SqueezeNet 기반 SSD 모델을 생성합니다.
else:
    print("The net type is wrong. It should be one of vgg16-ssd, mb1-ssd and mb1-ssd-lite.")
    # 네트워크 타입이 잘못되었음을 출력합니다.
    sys.exit(1)

# 모델 파일을 로드합니다.
net.load(model_path)

# 4. 네트워크 지정
if net_type == 'vgg16-ssd':
    predictor = create_vgg_ssd_predictor(net, candidate_size=200)
    # VGG16 기반 SSD 모델의 예측기를 생성합니다.
elif net_type == 'mb1-ssd':
    predictor = create_mobilenetv1_ssd_predictor(net, candidate_size=200)
    # MobileNetV1 기반 SSD 모델의 예측기를 생성합니다.
elif net_type == 'mb1-ssd-lite':
    predictor = create_mobilenetv1_ssd_lite_predictor(net, candidate_size=200)
    # MobileNetV1-Lite 기반 SSD 모델의 예측기를 생성합니다.
elif net_type == 'mb2-ssd-lite':
    predictor = create_mobilenetv2_ssd_lite_predictor(net, candidate_size=200)
    # MobileNetV2-Lite 기반 SSD 모델의 예측기를 생성합니다.
elif net_type == 'sq-ssd-lite':
    predictor = create_squeezenet_ssd_lite_predictor(net, candidate_size=200)
    # SqueezeNet 기반 SSD 모델의 예측기를 생성합니다.
else:
    predictor = create_vgg_ssd_predictor(net, candidate_size=200)
    # 잘못된 네트워크 타입이 들어올 경우 기본 예측기를 생성합니다.

# 5. 이미지 파일을 읽고 추론을 위해 변환
orig_image = cv2.imread(image_path)  # 이미지 파일을 읽습니다.
image = cv2.cvtColor(orig_image, cv2.COLOR_BGR2RGB)  # 이미지를 BGR에서 RGB로 변환합니다.

# 6. 모델에 이미지 파일을 입력하여 추론
boxes, labels, probs = predictor.predict(image, 10, 0.4)
# 이미지를 입력으로 하여 모델에서 객체를 추론합니다. 
# boxes: 탐지된 객체의 경계 상자
# labels: 객체의 클래스 레이블
# probs: 객체의 확률(신뢰도)

# 7. 추론 결과인 박스와 클래스 명을 이미지에 표시
for i in range(boxes.size(0)):
    box = boxes[i, :]  # 현재 객체의 경계 상자를 가져옵니다.

    # 이미지에 객체의 경계 상자를 그립니다.
    cv2.rectangle(orig_image, (int(box[0]), int(box[1]), int(box[2]), int(box[3])), (255, 255, 0), 4)
    # 레이블과 확률을 문자열로 포맷하여 출력합니다.
    label = f"{class_names[labels[i]]}: {probs[i]:.2f}"
    Data = "classID:{} box({}, {}, {}, {}) conf:{}".format(labels[i], int(box[0]), int(box[1]), int(box[2]), int(box[3]), probs[i])
    print(Data)  # 콘솔에 객체 정보 출력
    # 이미지에 레이블을 추가합니다.
    cv2.putText(orig_image, label,
                (int(box[0]) + 20, int(box[1]) + 40),    
                cv2.FONT_HERSHEY_SIMPLEX,
                1,  # 글꼴 크기
                (255, 0, 255),
                2)  # 선 두께

# 8. 결과 이미지 파일 저장
path = "run_ssd_example_output.jpg"  # 결과 이미지 파일의 저장 경로
cv2.imwrite(path, orig_image)  # 이미지를 파일로 저장합니다.
print(f"Found {len(probs)} objects. The output image is {path}")  # 탐지된 객체의 수와 결과 이미지 파일 경로를 출력합니다.

```


inference_ssd_windows.py
-------------------------
SSD 사물인지 CCTV 추론코드
영상파일 추론시 사용합니다.

```python

# jetsonai.learning@gmail.com
# 20230630

cam_str = 0

import cv2
import numpy as np
# 시스템, opencv, 그리고 하위 디렉토리 vision에 있는 ssd 패키지 임포트
from vision.ssd.vgg_ssd import create_vgg_ssd, create_vgg_ssd_predictor
from vision.ssd.mobilenetv1_ssd import create_mobilenetv1_ssd, create_mobilenetv1_ssd_predictor
from vision.ssd.mobilenetv1_ssd_lite import create_mobilenetv1_ssd_lite, create_mobilenetv1_ssd_lite_predictor
from vision.ssd.squeezenet_ssd_lite import create_squeezenet_ssd_lite, create_squeezenet_ssd_lite_predictor
from vision.ssd.mobilenet_v2_ssd_lite import create_mobilenetv2_ssd_lite, create_mobilenetv2_ssd_lite_predictor
from vision.utils.misc import Timer

#  opencv, system 패키지 임포트
import cv2
import sys

# 객체인식 결과를 이미지에 표시하는 함수
def imageProcessing(frame, predictor, class_names):
    # 추론을 위해 변환
    image = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)

    # 모델에 이미지 파일을 입력하여 추론
    boxes, labels, probs = predictor.predict(image, 10, 0.4)
    # 추론 결과인 박스와 클래스 명을 이미지에 표시
    for i in range(boxes.size(0)):
        # 신뢰도 0.5 이상의 박스만 표시
        if(probs[i]>0.5):
            # 바운딩박스 표시			
            box = boxes[i, :].detach().cpu().numpy().astype(np.int64)
            cv2.rectangle(frame, (box[0], box[1]), (box[2], box[3]), (255, 255, 0), 4)
            label = f"{class_names[labels[i]]}: {probs[i]:.2f}"
            cv2.putText(frame, label,
                (box[0] + 20, box[1] + 40),
                cv2.FONT_HERSHEY_SIMPLEX,
                1,  # 폰트크기
                (255, 0, 255),
                2)  # 선의 유형


    return frame

# 영상 딥러닝 프로세싱 함수
def videoProcess(openpath, model_path, label_path):
    # 라벨 파일을 읽어 클래스 이름들 세팅
    class_names = [name.strip() for name in open(label_path).readlines()]

    # 모델 파일을 읽어 모델 로딩
    net = create_mobilenetv1_ssd(len(class_names), is_test=True)

    net.load(model_path)

    # 네트워크 지정
    predictor = create_mobilenetv1_ssd_predictor(net, candidate_size=200)

    # 카메라나 영상으로부터 이미지를 갖고오기 위해 연결 열기
    cap = cv2.VideoCapture(openpath)
    if cap.isOpened():
        print("Video Opened")
    else:
        print("Video Not Opened")
        print("Program Abort")
        exit()

    # 영상보여주기 위한 opencv 창 생성
    cv2.namedWindow("Output", cv2.WINDOW_GUI_EXPANDED)

    try:
        while cap.isOpened():
            # 이미지 프레임 읽어오기
            ret, frame = cap.read()
            if ret: 
                # 이미지 프로세싱 진행한 후 그 결과 이미지 보여주기			
                result = imageProcessing(frame, predictor, class_names)
                cv2.imshow("Output", result)
            else:
                break

            if cv2.waitKey(int(1000.0/120)) & 0xFF == ord('q'):
                break

    except KeyboardInterrupt:  
        print("key int")
        cap.release()
        cv2.destroyAllWindows()
        return
    # 프로그램 종료 후 사용한 리소스를 해제한다.
    cap.release()

    cv2.destroyAllWindows()

    return
   
# 인자가 3보다 작으면 종료, 인자가 3면 카메라 추론 시작, 인자가 3보다 크면 영상파일 추론
if len(sys.argv) < 3:
    print('Usage: python run_ssd_example.py <model path> <label path> <image path>')
    sys.exit(0)

if len(sys.argv) == 3:
    gst_str = cam_str
    print("camera 0")

else:
    gst_str = sys.argv[3]
    print(gst_str)

model_path = sys.argv[1]
label_path = sys.argv[2]

# 영상 딥러닝 프로세싱 함수 호출
videoProcess(gst_str, model_path, label_path)

```

## 사진 파일 추론
```
python3 run_ssd_example.py mb1-ssd ./models/ssd_example/ssd_cctv_sample.pth ./models/ssd_example/labels.txt ./data/drivingcar.jpg
```
![run_ssd_example_output](https://github.com/user-attachments/assets/7a937977-5742-40f1-960f-38e27a556b14)



## 영상 파일 추론
```
python3 inference_ssd_windows.py ./models/ssd_example/ssd_cctv_sample.pth ./models/ssd_example/labels.txt ./data/run3.mp4
```
![run_ssd_example_output_run3_mp4](https://github.com/user-attachments/assets/dc608e38-81b9-4be9-88d3-c5a455dafcf7)

