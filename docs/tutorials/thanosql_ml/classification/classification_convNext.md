# __이미지 분류 모델 만들기__

**[이전 문서 - Auto-ML을 사용하여 분류 모델 만들기](/tutorials/thanosql_ml/classification/automl_classification/)** <br>
**[다음 문서 - 텍스트 분류 모델 만들기](/tutorials/thanosql_ml/classification/classification_Electra/)**

## 시작 전 사전 정보

- 튜토리얼 난이도: ★☆☆☆☆
- 읽는 시간 : 10분
- 사용 언어 : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 참고 문서: [(캐글) Cat and Dog 데이터 세트](https://www.kaggle.com/datasets/tongpython/cat-and-dog), [A ConvNet for the 2020s](https://arxiv.org/abs/2201.03545)
- 마지막 수정날짜 : 2022-06-01


## 튜토리얼 소개

!!! note "분류 작업 이해하기"
    분류 작업은 목표값(Target)이 속한 범주(Category 또는 Class)를 예측하기 위해 사용하는 [머신러닝(기계학습/Machine Learning)](https://ko.wikipedia.org/wiki/%EA%B8%B0%EA%B3%84_%ED%95%99%EC%8A%B5)의 한 형태입니다. 예를 들어, 남성 또는 여성을 분류하는 이진 분류와 동물의 종(개, 고양이, 토끼 등)을 예측하는 다중 분류 모두 분류 작업에 포함됩니다.

2010년부터 인공지능 모델을 사용해서 이미지를 분류하는 대회([ImageNet](https://en.wikipedia.org/wiki/ImageNet))가 열려 왔습니다. 대회 초기 우승한 모델의 분류 성능은 약 72%였으나 2015년 우승한 [ResNet](https://arxiv.org/abs/1512.03385) 모델은 약 96%의 성능을 기록하며 특정 영역에서는 인간의 분류 능력을 뛰어넘기 시작했습니다.

!!! tip ""
    같은 데이터의 인간의 분류 능력은 약 95%라고 합니다.

정확한 이미지 분류를 위해서는 대량의 데이터 세트에 대한 [데이터 라벨링](https://en.wikipedia.org/wiki/Labeled_data) 작업이 필요하지만, 사전 학습된 인공지능 모델의 가중치를 사용하여 소량의 라벨링 된 데이터 세트에 맞게 재보정해서 사용하는 방법들이 널리 적용되고 있습니다. 결과적으로 비교적 적은 수의 데이터를 가지고도, 딥러닝 모델의 훈련을 가능하게 합니다.

ThanoSQL에서는 다양한 사전 학습된 인공지능 모델을 제공하고 있으며, 간단한 쿼리 구문을 통해 모델을 만들 수 있게 제공합니다. 이를 통해, 사용자는 적절하게 학습된 이미지 분류 모델로부터 특징을 정량화하기 힘든 이미지로부터 잠재적인 인사이트를 추출하고 다양한 서비스에 활용할 수 있습니다.

__아래는 ThanoSQL 이미지 분류 모델의 활용 및 예시입니다.__

-  ThanoSQL 이미지 분류 모델은 온라인 상품 판매 서비스에서 상품 등록을 위한 적절한 범주를 찾는 과정의 반복을 줄여줍니다. 간단한 쿼리 구문만으로 제품 사진의 범주를 분류할 수 있습니다. 사용자는 일부 잘못 분류 된 데이터를 수정하는 작업만을 진행함으로써 기존 이미지 분류에 사용하던 시간을 절약할 수 있습니다.

- 미술 작품의 대여 또는 판매 서비스를 하는 경우 각 작품의 느낌, 기법, 어울리는 장소 등 기준이 모호하여 분류하기 어려운 작업들을 이미지 분류 모델을 사용해 대략적으로 분류할 수 있습니다.

- 제조 공장에서 육안으로 확인하던 스크래치나 파손과 같은 불량품을 감지하고 분류할 수 있습니다. 레이저 스펙트럼과 같은 신호 정보도 시각화 변환과 같은 처리를 통해서 이미지 분류 모델에 적용할 수 있습니다.

!!! tip ""
    미술 작품을 좋아하는 사람들의 행동 이력(구매 또는 대여 이력, 선호도 등)을 데이터 베이스에 저장하고 활용해서 특정 작품을 좋아할 것으로 예상되는 그룹을 찾는 분류 모델을 만들 수도 있습니다. 즉, 미술 작품의 이미지만을 이용해서 연령대(20대, 30대, 40대 등), 성별(남, 여), 전시 장소(집, 카페, 회사 등) 등에서의 선호도를 예측하는 모델을 만들 수 있습니다.


!!! note "본 튜토리얼에서는"
    :point_right: 대표적인 머신러닝 경진대회 플랫폼인 [캐글](https://www.kaggle.com/)의 `Cat and Dog` 데이터 세트를 사용하여 고양이와 강아지를 분류하는 모델을 만듭니다. 이 데이터 세트는 25,000장의 훈련 데이터와 12,500장의 테스트 데이터로 구성되어 있습니다. 직관적으로 이해하기 쉽고 각 이미지의 크기가 크지 않아 이미지 분류 모델의 정확도를 판단하기 위해 널리 사용됩니다. 본 튜토리얼에서는 ThanoSQL의 사용방법을 익히고 빠른 결과 확인을 위해, 훈련용 데이터 8,000장과 테스트 데이터 2,000장만을 사용합니다. <br>

!!! warning "튜토리얼 주의 사항"
    - 이미지 분류 모델은 하나의 이미지에서 하나의 목표값(Target, 범주/레이블/라벨)를 예측하는 용도로 사용할 수 있습니다.
    - 이미지의 경로를 나타내는 컬럼(Column)과, 이미지의 목표값을 나타내는 컬럼이 존재해야 합니다.
    - 해당 이미지 분류 모델의 베이스 모델(`CONVNEXT`)은 GPU를 사용합니다. 사용한 모델의 크기와 배치 사이즈에 따라 GPU 메모리가 부족할 수 있습니다. 이 경우, 더 작은 모델을 사용하시거나 배치 사이즈를 줄여보십시오.

## __1. 데이터 세트 확인__

본 튜토리얼을 진행하기 위해 우리는 ThanoSQL DB에 저장되어 있는  <mark style="background-color:#FFEC92 ">cat_and_dog_train</mark> 테이블을 사용합니다. 아래의 쿼리문을 실행하여 테이블 내용을 확인합니다.

```sql
%%thanosql
SELECT *
FROM cat_and_dog_train
LIMIT 5
```
![IMAGE](/img/thanosql_ml/classification/classification_convNext/train_data_limit_5.png)

!!! note "__데이터 이해하기__"
    -  <mark style="background-color:#D7D0FF ">image</mark>: 각 이미지의 파일의 위치 정보
    -  <mark style="background-color:#D7D0FF ">label</mark>: 해당 이미지의 목표값(Target)

```sql
%%thanosql
PRINT IMAGE AS
SELECT image FROM cat_and_dog_train
LIMIT 5
```

![IMAGE](/img/thanosql_ml/classification/classification_convNext/print_image_train_data.png)

## __2. 사전 학습된 모델을 사용하여 강아지와 고양이 이미지 분류 결과 예측__

먼저 저희가 미리 준비해둔 모델로 바로 결과를 예측해보겠습니다. 다음 쿼리문을 실행하면, 사전에 학습을 해 둔 고양이, 개 이미지 분류모델, <mark style="background-color:#E9D7FD ">tutorial_image_classification</mark>모델을 사용하여 결과를 예측해볼 수 있습니다.

```sql
%%thanosql
PREDICT USING tutorial_image_classification
AS SELECT * FROM cat_and_dog_test
```

![IMAGE](/img/thanosql_ml/classification/classification_convNext/predict_on_test_data_1.png)

## __3. 이미지 분류 모델 생성__

이전 단계에서 확인한  <mark style="background-color:#FFEC92 ">cat_and_dog_train</mark> 데이터 세트를 사용하여 이미지 분류 모델을 만듭니다. 아래의 쿼리 구문을 실행하여 <mark style="background-color:#E9D7FD ">my_image_classifier</mark>이라는 이름의 모델을 만듭니다.

```sql
%%thanosql
BUILD MODEL my_image_classifier
USING ConvNeXt_Tiny
OPTIONS (
  image_col='image',
  label_col='label',
  epochs=1
  )
AS
SELECT *
FROM cat_and_dog_train
```

!!! note "쿼리 세부 정보"
    - "__BUILD MODEL__" 쿼리 구문을 사용하여 <mark style="background-color:#E9D7FD ">my_image_classifier</mark> 모델을 만들고 학습시킵니다.
    - "__USING__" 쿼리 구문을 통해 베이스 모델로 `ConvNeXt_Tiny`를 사용할 것을 명시합니다.
    - "__OPTIONS__" 쿼리 구문을 통해 모델 생성에 사용할 옵션을 지정합니다.
        - "image_col" : 이미지 경로를 담은 열의 이름
        - "label_col" : 목표값의 정보를 담은 열의 이름
        - "epochs : 모든 학습 데이터 세트를 학습하는 횟수

!!! tip ""
    여기서는 빠르게 학습하기 위해 "epochs"를 1로 지정했습니다. 일반적으로 숫자가 클수록 많은 계산 시간이 소요되지만 학습이 진행됨에 따라 예측 성능이 올라갑니다.


## __4. 생성된 모델을 사용하여 강아지와 고양이 이미지 분류 결과 예측__

이전 단계에서 만든 이미지 예측 모델(<mark style="background-color:#FFEC92 ">my_image_classifier</mark>)을 사용해서 특정 이미지(학습에 이용되지 않은 데이터 테이블, <mark style="background-color:#D7D0FF">cat_and_dog_test</mark>)의 목표값을 예측해 봅니다.  아래 쿼리를 수행하고 나면, 예측 결과는 <mark style="background-color:#D7D0FF">predicted</mark> 열에 저장되어 반환됩니다.

```sql
%%thanosql
PREDICT USING my_image_classifier
OPTIONS (image_col='image')
AS
SELECT *
FROM cat_and_dog_test
```

![IMAGE](/img/thanosql_ml/classification/classification_convNext/predict_on_test_data_2.png)

!!! note "__쿼리 세부 정보__"
    - "__PREDICT USING__" 쿼리 구문을 통해 이전 단계에서 만든 <mark style="background-color:#E9D7FD ">my_image_classifier</mark> 모델을 예측에 사용합니다.
    - "__OPTIONS__" 쿼리 구문을 통해 예측에 사용할 옵션을 지정합니다.
        - "image_col" : 예측에 사용할 이미지의 경로가 기록되어 있는 열의 이름

<br>

## __5. 튜토리얼을 마치며__

이번 튜토리얼에서는 <mark style="background-color:#FFD79C">Cat and Dog</mark> 데이터 세트를 사용하여 이미지 분류 모델을 만들어 보았습니다. 초급 단계 튜토리얼인만큼 정확도 향상을 위한 과정 설명보다는 작동 위주의 설명으로 진행했습니다. 이미지 분류 모델은 각 플랫폼이나 서비스에 맞는 정밀한 튜닝을 통해 정확도를 향상 시킬 수 있고 적은 양의 데이터 라벨링만으로도 대부분 만족스러운 결과를 얻을 수 있습니다. 나만의 데이터를 이용해서 베이스 모델을 학습하거나, 자가학습(Self-supervised) 모델 등을 이용해 나의 데이터를 수치화하여 변환한 후 자동화 된 머신러닝(Auto-ML) 기법을 이용한 배포도 가능합니다. 다양한 비정형 데이터(오디오, 비디오, 텍스트 등)와 수치형 데이터들을 결합하여 나만의 모델을 만들고 경쟁력있는 서비스를 제공해 보세요.

다음단계인  [중급 이미지 분류 모델 만들기](comingsoon) 튜토리얼에서는 이미지 분류 모델을 더욱 심도있게 다뤄봅니다. 내 서비스를 위한 나만의 이미지 분류 모델 구축방법에 대해 더욱 자세히 알고 싶다면 다음 튜토리얼들을 진행해보세요.

* [나만의 데이터 업로드하기](comingsoon)
* [중급 이미지 분류 모델 만들기](comingsoon)
* [이미지 변환과 Auto-ML을 이용한 나만의 모델 만들기](comingsoon)
* [나만의 이미지 분류 모델 배포하기](comingsoon)


!!! tip "__나만의 서비스를 위한 모델 배포 관련 문의__"
    ThanoSQL을 활용해 나만의 모델을 만들거나, 나의 서비스에 적용하는데 어려움이 있다면 언제든 아래로 문의주세요😊

    이미지 분류 모델 구축 관련 문의: contact@smartmind.team


