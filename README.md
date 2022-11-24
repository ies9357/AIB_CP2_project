# Keras와 pytorch를 활용한 피부암 이미지 분할/분류 딥러닝 모델 제작 및 최적화

## 1. 프로젝트에서 풀고자 하는 문제 : **생명에 치명적인 피부암[Ex. 흑색종 악성]에 의한 환자의 사망 예방**
1. 문제 인식
    1. 여러 피부암의 종류들 중에 **흑색종 악성** 같은 경우에는 다른 **뼈나 혈관으로의 암세포의 전이**를 쉽게 일으키기 때문에 **사망확률**이 다른 피부암들에 비해 **높다.**
    2. **흑색종 악성**은 육안으로 **모반(일반 점)과 생김새가 비슷**하여 **조기**에 **발견**하기도 **어렵다**.
    
2. 문제 정의
    1. **흑색종 악성과 같은 생명에 치명적인 피부암**을 발견하지 못하여 **치료시기를 놓침으로 인한 피부암 환자의 사망** 예방

3. 가설
    1. CNN 기반의 **피부암 이미지 분할/분류 딥러닝 모델을 최적화 함**으로써, **일반 점[모반]과 흑색종 악성 및 기타 피부암들을 잘 분류**하여 **분류 결과에 맞게 피부암을 치료**하고 환자의 **사망을 예방**할 수 있다.
        
## 2. 프로젝트 진행과정 **(기간 : 2022.09.15 - 2022.10.14)**
1. 데이터 탐색 및 선택
2. PyTorch 기초지식 및 이미지 segmentation 공부
3. 데이터 셋 EDA 및 전처리
4. 최신논문 검색 및 내용 정리
5. 이미지 분할/분류 딥러닝 모델 구현 및 최적화
6. 이미지 분할모델로 Segmented된 피부암 이미지 제작 후, e의 과정 반복
7. 결과 정리 및 최종 보고서 작성
8. 발표자료 작성 & 촬영

## 3. 데이터 셋 설명
1. 데이터 : Ham10000 피부암 데이터 셋
2. 출처 : [https://bit.ly/3VkAIgg](https://bit.ly/3VkAIgg)

      ![image](https://user-images.githubusercontent.com/102272580/203705788-be6c2711-8d46-468e-9a75-72ba93c9a00e.png)


3. 이미지 데이터 개수 및 크기
    1. 이미지 데이터[왼쪽 이미지] 개수 : 10015개
    2. 마스크 데이터[오른쪽 이미지 - 검정색, 흰색 바탕] 개수 : 10015개
    3. 이미지 및 마스크 크기(Pixels) : 450(높이) X 600(너비) X 3(채널 수)

4. 피부암 라벨의 종류
    1. **원래는** 흑색종, 모반, 기타 피부암 들을 포함하여 **7가지 존재**
    2. **흑색종, 모반 이외의 피부암들**은 데이터 개수가 작기 때문에 **하나의 라벨[기타 피부암(Other_SC)]로 통합**

        ![image](https://user-images.githubusercontent.com/102272580/203706058-e4f135b0-f4a9-4660-a6ee-4cd782538e52.png)

5. 라벨 별 데이터 개수 및 비율
    1. 모반에 치우쳐진 **불균형 데이터** 임을 알 수 있다.
    
        1. 테이블


        | 라벨 | 개수(개) | 비율(%) |
        | --- | --- | --- |
        | 흑색종 악성( MEL) | 1113 | 11.11 |
        | 모반 (NV) | 6705 | 66.95 |
        | 기타 피부암 (Other _SC) | 2197 | 21.94 |
        

        2. 파이차트


![image](https://user-images.githubusercontent.com/102272580/203715192-d2e9a693-b4bb-4764-9206-0f7b7df10e44.png)


## 4. 문제상황 해결과정
1. 피부암 이미지에 대한 분할/분류 딥러닝 모델 제작
    1. Unet 기반 피부암 이미지 내 병변 분할모델
        1. 데이터 전처리
            1. 이미지를 학습모델에 일괄적으로 불러올 수 있도록 경로설정
            2. 학습효율 개선을 위해 이미지 자체에 대한 전처리[이미지 사이즈 변경, 정규화 등…] 진행
            3. 사용 라이브러리 : os, pandas, matplotlib.pyplot, cv2, Image, numpy

        2. 모델 구성
            1. 사용모델 종류 : Unet 모델(학습할 이미지 사이즈에 맞게 customizing 진행)
            2. Unet 모델 구조
                1. Downsampling 부분(이미지 특징 추출) : Conv2D, Dropout, MaxPooling2D 로 구성됨
                2. Upsampling 부분(원본 이미지 크기 복원) : Conv2DTranspose, Concatenate, Conv2D, Dropout 사용
            3. 사용 하이퍼파라미터
                1. **Input 이미지 크기(Pixels)** : **448**(높이) X **592**(너비) X **3**(채널 개수) → Unet모델에 동작하도록 하되, 원본 이미지의 크기는 최대한 유지
                2. **Dropout** : 0.3 / **Batch Size** : 16 / **Epochs** : 30

    2. PyTorch 기반 피부암 이미지 분류모델
        1. 데이터 전처리
            1. 이미지를 학습모델에 일괄적으로 불러올 수 있도록 경로설정
            2. 학습효율 개선을 위해 이미지 자체에 대한 전처리[이미지 사이즈 변경, 정규화 등…] 진행
            3. 사용 라이브러리 : os, pandas, matplotlib.pyplot, cv2, Image, numpy, torchvision.transforms, torchvision.datasets.ImageFolder, torch.utils.data.DataLoader 등…
        2. 모델 구성
            1. 사전 학습모델 resnet50을 기반으로 output 부분만 라벨 수에 맞게 살짝
            바꿔 학습 진행
            2. 사용 하이퍼파라미터
                1. Criterion : CrossEntropy
                2. optimizer : sgd (학습률 : 0.001, momentum : 0.9)
                3. epoch = 999 (patience = 50, 평가지표 = valid_loss)
                4. batch_size = 64 (train, validation, test 셋)

2. 제작한 딥러닝 모델을 이용한 이미지 분류

![image](https://user-images.githubusercontent.com/102272580/203715310-f821e5ed-8528-40a2-b4cf-677f05ea1b29.png)

3. 딥러닝 분류 결과에 맞는 병원 방문 및 치료법 적용
    
## 5. 프로젝트 결과
1. 이미지에 대한 분할/분류 모델을 keras와 pytorch을 통해 구현
2. 제작한 피부암 분할모델의 경우, 병변과 주변피부 사이의 분할을 어느정도 잘 수행
    1. 이미지 내의 **Noise**[ex. 털]가 **없는 경우** : 마스크 이미지와 거의 흡사하게 병변을 분할함
    2. Noise가 있는 경우 : 마스크 이미지와 윤곽이 살짝 다르게 나타나는 경우가 많음
    3. IoU score : 임계값 0.4 기준 **0.7149**
3. **제작한 피부암 분류모델**이 **흑색종을 분류**하여 **사망률을 낮추는 데** 있어 어느 정도는 **도움**이 됨
    1. 전체 분류 정확도 : 0.8504
    2. 흑색종 F1-score : 0.70
    3. 흑색종 Recall : 0.88
4. 그러나, **피부암 병변에 대한 분할을 진행 후**에 **분류**를 하는 것이 분류모델의 **성능 개선에는 도움이 되지 않음**

## 6. 한계점 및 해결방안
1. 한계점
    1. 딥러닝 관련 배경지식이 여전히 부족함을 느껴 논문 이해에 어려움을 겪어서 시간이 많이 지체되었다.
    2. 고생고생해서 논문에 나와있는 모델을 돌렸을 때 런타임이 엄청나게 길거나 램이 터지는 이슈 등이 발생해서 논문에 나와있는 코드를 간소화 했는데 성능이 잘 나온 것 같지 않았다.
    3. PyTorch 활용경험이 많지 않아 PyTorch를 이용한 image segmentation은 구현하지 못했으며, 구현한 PyTorch 기반 딥러닝 분류모델의 경우에는 성능 개선을 위해 어떤 부분을 개선해야 할 지도 헷갈렸다.
2. 해결방안
    1. 딥러닝 관련 기본지식 및 PyTorch 활용경험을 더 쌓는다.
    2. 딥러닝 모델에 대한 경량화 수행
    3. 코랩프로로 업그레이드 하거나 더 좋은 gpu를 가진 컴퓨터를 구입한다.
