# 피부 질환 이미지 분류
<p align="center"><img width="574" alt="스크린샷 2023-05-03 오후 4 46 55" src="https://user-images.githubusercontent.com/123061697/235858817-751b8c12-0823-458b-bd65-36481d4f9956.png" width="10" height="250"></p>

----


## INDEX

1. [프로젝트 소개](#1-프로젝트-소개)
2. [EDA](#2-eda)
3. [모델링](#3-모델링)
4. [성능개선](#4-성능개선)
5. [최종모델](#5-최종모델)
6. [REVIEW](#6-review)

----

## 1. 프로젝트 소개
### 1-1. 프로젝트 기간
2023.04.05 ~ 2023.04.26

### 1-2. 주제선정 및 목표
- 주제선정
  - 코로나19를 계기로 주목받은 원격의료가 피부질환 진단에서도 존재감을 키우고 있고 '텔레더마톨로지(원격피부질환진료)'라고 불리는 새로운 분야로 자리잡으며 의학계에서도 관련 연구가 쏟아지고 있습니다. 텔레더마톨로지 기술에 대한 연구가 늘어난 이유는 대면진료가 중요한 피부질환 진단 분야가 코로나19로 인해 어쩔 수 없는 비대면 진료 상황에 놓였기 때문입니다. 피부질환은 피부확대경과 같은 장비를 사용해 의사가 직접 눈으로 병변을 살피면서 정확하게 확인해야 하지만 비대면 진료가 불가피한 상황에서 큰 어려움을 겪게 되었고 이에따라 인공지능(AI) 등을 활용한 비대면 진료기술 발전에 힘을 쏟아야 한다는 목소리가 높아지고 있습니다.
  - 따라서 10가지 피부질환 이미지 분류가 가능한 모델을 통해 원격진료 정확도 상승 및 자가진단이 가능하게 하는것이 프로젝트의 목표입니다.
  
-----

## 2. EDA
### 2-1. 데이터 소개
- 출처 : kaggle(피부 종양 데이터)
- class : 10개
  - 1\. Eczema
  - 2. Melanoma
  - 3. Atopic Dermatiti
  - 4. Basal Cell Carcinoma (BCC)
  - 5. Melanocytic Nevi (NV)
  - 6. Benign Keratosis-like Lesions (BKL)
  - 7. Psoriasis pictures Lichen Planus and related deseases
  - 8. Seborrheic Keratoses and other Benign Tumors
  - 9. Tinea Ringworm Candidiasis and other Fungal Infections
  - 10\. Warts Molluscum and other Viral Infections


<img width="531" alt="스크린샷 2023-05-03 오후 9 04 41" src="https://user-images.githubusercontent.com/123061697/235910808-0d053100-6e92-42c4-b00b-727a5af01dbd.png">

- 용량 : 총 5GB(27,153장)

### 2-2. class 개수 확인

<img width="799" alt="스크린샷 2023-05-03 오후 9 14 25" src="https://user-images.githubusercontent.com/123061697/235912641-59dace08-2b98-4539-b26e-f6e120e01e07.png">

----

## 3. 모델링
### 3-1. 6가지 모델 성능 비교

- 모델 -> EfficientNetB5 / VGG16 / ResNet152V2 / ResNet50V2 / DenseNet201 / Xception
- split -> Train : Validation : Test = 8 : 1 : 1
- augmentation -> rotation_range=30   /  width_shift_range=0.1  /  height_shift_range=0.1  /  shear_range=0.15  /  horizontal_flip=True
- Include_top=False   /  optimizer=’adam’  /  loss=’categorical_crossentropy  /  metrics=[’accuracy’]

<img width="535" alt="스크린샷 2023-05-03 오후 9 33 14" src="https://user-images.githubusercontent.com/123061697/235917125-333778e6-dc40-45b4-bb7e-75944cc2cc74.png">

- 증강 후 accuracy가 하락하는 것을 알 수 있었다
- 의료 데이터 특성상 국소적인 부분에 병변이 있고 증강 과정에서 제거 또는 변형되었을 수 있다고 예상했고 이에따라 augmentation을 수정하는 과정이 필요할 것이라고 판단했다
- 또한 현재의 데이터로는 정상 이미지는 판단하지 못해 정상 피부 데이터를 수집해 추가할 예정이다
- Test accuracy 기준 성능이 가장 좋고 증강 후 accuracy는 낮아졌지만 과적합이 줄어드는 경향이 있는 EfficientNetB5를 최종모델로 선정하였다

< EfficientNetB5 증강 전 loss, accuracy >

<img width="527" alt="스크린샷 2023-05-03 오후 9 54 11" src="https://user-images.githubusercontent.com/123061697/235922148-10531f78-a3a1-4e14-984c-c160f991c3fb.png">

< EfficientNetB5 증강 전 confusion matrix, classification report >

<img width="529" alt="스크린샷 2023-05-03 오후 9 56 54" src="https://user-images.githubusercontent.com/123061697/235922719-e1a569c7-4d87-4db5-b9af-6c409ddf4b13.png">

- 클래스별로 f1 score가 차이나는 편이며 confusion matrix를 확인해봤을 때 특정 클래스들끼리 헷갈려 하는 것으로 보였다

----

## 4. 성능개선
### 4-1. 정상 데이터 추가
- 정상 피부 데이터 크롤링 등으로 수집
- 0번 클래스로 추가(1000장)

<img width="529" alt="스크린샷 2023-05-03 오후 10 03 24" src="https://user-images.githubusercontent.com/123061697/235923985-0d3f8dd0-4d52-433b-b2e0-953415a80332.png">

### 4-2. 판별 불가한 데이터 제거
- 원본 데이터를 살펴보았을 때 피부 표면이 아닌 이미지가 있어 제거
- 판별 불가한 사진 예시

<img width="529" alt="스크린샷 2023-05-03 오후 10 10 05" src="https://user-images.githubusercontent.com/123061697/235925487-cdcef402-3494-413e-8347-31239cd87014.png">

### 4-3. Augmentation 수정
- 병변이 사라질 위험이 있는 crop, zoom과 관련된 증강 삭제
- rotation, horizontal 등만 수행

### 4-4. 성능 확인

- Train_Acc : 76%(+3%), Val_acc : 75%(+2%), Test_acc : 76%(+3%)

< EfficientNetB5 loss, accuracy >

<img width="722" alt="스크린샷 2023-05-03 오후 10 20 12" src="https://user-images.githubusercontent.com/123061697/235928653-ab872527-8ab2-4c74-bce0-8ab888c66bbc.png">

----

## 5. 최종모델
### 5-1. Fine tunning
- 모델의 성능을 향상시키기 위해 출력층 근처에 있는 상위 30개 층을 동결해제하고 미리 학습한 가중치 대신 가지고 있는 데이터로 층을 학습
- Fine tunning 전 후 비교

<img width="382" alt="스크린샷 2023-05-03 오후 10 27 22" src="https://user-images.githubusercontent.com/123061697/235929787-aa5c5c22-10bc-42f5-aeab-16a706b90d71.png">

- early stopping과 reduce lr을 callback함수로 사용해 총 58 epoch를 수행
- Fine tunning 과정을 거친 후 최종적으로 Test accuracy 80% 의 성능을 보임

### 5-2. 결과

<img width="498" alt="스크린샷 2023-05-03 오후 10 30 43" src="https://user-images.githubusercontent.com/123061697/235930677-bd038e56-60d3-407c-9a3c-659ee59fe76c.png">

----

## 6. REVIEW
```
- 다른 클래스에 비해 5번의 개수가 좀 더 많아 다운 샘플링을 진행해보았어도 좋았을 것 같다
- 8, 9, 10번같은 클래스에는 한개의 병변이 아닌 기타로 묶여 여러개의 병변이 있다보니 모델링 시 
학습에 방해가 되는 요소로 작용했을 수 있다는 점이 아쉬웠다
  - 이 부분은 병변을 가지고 임의로 분류할 수 없어 그대로 진행했다
- 의료 데이터의 특성에 따라 증강을 다르게 해야 한다는 점을 알게 되었고 데이터의 검수 및 전처리 과정이 중요하다는 점을 다시한번 깨닫게 되었다
```

----
TEAM
- 고지현
- 윤정옥
- 황지우
