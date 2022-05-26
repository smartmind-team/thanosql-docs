# 이미지 분류 모델 만들기

## (ThanoSQL 무작정 따라하기) 고양이와 강아지를 분류하는 이미지 분류 모델 만들기

**[이전 문서 - ]()** <br>| **[다음 문서 - 영화 리뷰에서 감정(긍정/부정) 분류 모델 만들기](https://github.com/smartmind-team/thanosql-docs/blob/dowon/docs/tutorials/thanosql_ml/text/classification/text_classification_kor_lv1.md)**

## 시작 전 사전 정보

- 튜토리얼 난이도: ★☆☆☆☆
- 읽는데 걸리는 시간: 10분
- 사용 언어 : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 실행 예제 파일 : 
- 참고문서: [(캐글) Cat and Dog 데이터 세트](https://www.kaggle.com/datasets/tongpython/cat-and-dog), [A ConvNet for the 2020s](https://arxiv.org/abs/2201.03545)
- 마지막 수정날짜 : 2022-06-01

<br>

## 튜토리얼 소개
> __분류 작업 이해하기__ <br>
> 분류 작업은 목표값(Target)이 속한 범주(Category 또는 Class)를 예측하기 위해 사용하는 [머신러닝(기계학습/Machine Learning)](https://ko.wikipedia.org/wiki/%EA%B8%B0%EA%B3%84_%ED%95%99%EC%8A%B5)의 한 형태입니다. 예를 들어, 남성 또는 여성을 분류하는 이진분류와 동물의 종(개, 고양이, 토끼 등)을 예측하는 다중 분류 모두 분류 작업에 포함됩니다.

인공지능 모델을 이용해서 이미지를 분류하는 대회([ImageNet](https://en.wikipedia.org/wiki/ImageNet))가 2010년부터 열려왔습니다. 2010년에 우승했던 모델의 분류 성능은 약 72% 입니다. 2015년 [ResNet](https://arxiv.org/abs/1512.03385)이라는 모델은 약 96%의 성능을 기록하면서 특정 영역에서는 인간의 분류 능력(해당 데이터의 경우 약 95%)를 뛰어넘기 시작했습니다. 정확한 이미지 분류를 위해서는 대량의 데이터 세트에 대한 [데이터 라벨링](https://en.wikipedia.org/wiki/Labeled_data) 작업이 필요하지만 사전학습 된 인공지능 모델을 이용해서 추가적인 학습을 진행함으로써 소량의 라벨링 된 데이터 세트만을 이용해서 우수한 성능을 낼 수 있는 방법들이 널리 적용되고 있습니다. ThanoSQL에서는 다양한 사전학습 된 인공지능 모델을 제공하고 있으며, 소량의 데이터 라벨링을 통해서도 쉽게 나만의 이미지 분류 모델을 만들 수 있도록 다양한 기능을 제공하고 있습니다. 일부 사전학습 된 모델은 도메인에 따라 별도의 학습과정이 없이도 키워드 검색이나 유사도 검색 등이 가능합니다. 적절하게 학습 된 이미지 분류 모델은 특성을 정량화하기 힘든 이미지 데이터로부터 원하는 인사이트를 추출하고 다양한 서비스에 활용할 수 있습니다.

__아래는 ThanoSQL 이미지 분류 모델의 활용 및 예시입니다.__
- 이미지 분류 모델을 사용하면 온라인 상품판매 서비스에서 상품 등록을 위해 매번 적절한 범주(카테고리)를 찾는 과정을 반복할 필요가 없습니다. 제품의 사진을 찍어서 ThanoSQL의 DB에 업로드하게 되면 이미지로부터 적절한 카테고리를 찾아서 자동으로 범주를 분류할 수 있습니다. 사용자는 일부 잘못 분류 된 데이터를 수정하는 작업만을 진행함으로써 이미지 분류에 사용하던 시간을 절약할 수 있습니다. 서비스를 지속하면서 데이터의 양이 많아지게 되면 알고리즘의 성능을 향상시킬 수 있고 성능 저하 여부를 모니터링하고 관리할 수 있습니다. 
- 미술 작품의 대여/판매 서비스를 하는 경우 각 작품의 느낌, 기법, 어울리는 장소 등 사람도 정확하게 분류하기 어려운 부분들을 이미지 분류 모델을 이용해서 대략적으로 분류할 수 있습니다. 해당 작품을 좋아하는 사람들의 행동이력(구매/대여 이력, 선호도 등)을 데이터베이스에 저장하고, 이러한 데이터를 이용해서 해당 작품을 좋아할 것으로 예상되는 그룹을 찾는 것을 정답이 되도록 분류 모델을 만들 수도 있습니다. 즉, 미술 작품의 이미지만을 이용해서 연령대(20대/30대/40대 등), 성별(남/여), 전시 장소(집/카페/회사 등) 등에서의 선호도를 예측하는 모델을 만들 수 있습니다. 
- 제조 공장 등에서 육안으로 확인하던 스크레치나 파손과 같은 불량품을 감지하고 분류 할 수 있습니다. 레이저 스펙트럼과 같은 신호 정보도 시각화 변환 등을 통해서 이미지 분류 모델을 적용할 수 있습니다. 이번 튜토리얼에서는 다루지 않지만 전체 이미지에서 특정 물체(Object)의 위치를 감지하고 경계선(Bounding Box)을 표현할 수 있습니다. 군중 속에서 마스크를 하지 않은 사람을 찾아내거나 자동차 전용 도로에서 보행자나 오토바이 등의 검출이 가능합니다. 

> __본 튜토리얼에서는__ <br>
> 대표적인 머신러닝 경진대회 플랫폼인 [캐글](https://www.kaggle.com/)의 `Cat and Dog` 데이터 세트를 사용하여 고양이와 강아지를 분류하는 모델을 만듭니다. 이 데이터 세트는 25,000장의 훈련 데이터와 12,500장의 테스트 데이터로 구성되어 있습니다. 직관적으로 이해하기 쉽고 각 이미지의 크기가 크지 않아 이미지 분류 모델의 정확도를 판단하기 위해 널리 사용됩니다. 본 튜토리얼에서는 ThanoSQL의 사용방법을 익히고 빠른 결과 확인을 위해, 훈련용 데이터 8,000장과 테스트 데이터 2,000장만을 사용합니다. <br>

> __튜토리얼의 의도 및 제한사항__ <br>
> - 이미지 분류 모델은 하나의 이미지에서 하나의 목표값(Target, 범주/레이블/라벨)를 예측하는 용도로 사용할 수 있습니다.
> - 이미지의 경로를 나타내는 컬럼(Column)과, 이미지의 목표값을 나타내는 컬럼이 존재해야 합니다.
> - 해당 이미지 분류 모델의 베이스 모델(`CONVNEXT`)은 GPU를 사용합니다. 사용한 모델의 크기와 배치 사이즈에 따라 GPU 메모리가 부족할 수 있습니다. 이 경우, 더 작은 모델을 사용하시거나 배치 사이즈를 줄여보십시오.

<br>

## 1. 데이터 세트 확인

본 튜토리얼을 진행하기 위해 우리는 ThanoSQL DB에 저장되어 있는 `cat_and_dog_train` 테이블을 사용합니다. 아래의 쿼리문을 실행하여 테이블 내용을 확인합니다. 

```python
%%thanosql
SELECT * 
FROM cat_and_dog_train 
LIMIT 5
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>image</th>
      <th>label</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>/data/Cat and Dog/training_set/training_...</td>
      <td>dog</td>
    </tr>
    <tr>
      <th>1</th>
      <td>/data/Cat and Dog/training_set/training_...</td>
      <td>dog</td>
    </tr>
    <tr>
      <th>2</th>
      <td>/data/Cat and Dog/training_set/training_...</td>
      <td>dog</td>
    </tr>
    <tr>
      <th>3</th>
      <td>/data/Cat and Dog/training_set/training_...</td>
      <td>dog</td>
    </tr>
    <tr>
      <th>4</th>
      <td>/data/Cat and Dog/training_set/training_...</td>
      <td>dog</td>
    </tr>
  </tbody>
</table>
</div>

> #### 데이터 이해하기
> - `image`: 각 이미지 파일의 위치 정보
> - `label`: 해당 이미지의 목표값(Target) <br>


> (향후 내용 추가 필요) 
> PRINT를 이용한 이미지 결과 샘플 출력

<br>

## 2. 이미지 분류 모델 만들기

이전 단계에서 확인한 `cat_and_dog_train` 데이터 세트를 사용하여 이미지 분류 모델을 만듭니다. 아래의 쿼리 구문을 실행하여 `my_image_classifier`이라는 이름의 모델을 만듭니다.

```python
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

    <Response [200]>

> #### 쿼리 세부 정보
> 
> "__BUILD MODEL__" 쿼리 구문을 사용하여 `my_image_classifier` 모델을 만들고 학습시킵니다. "__USING__" 쿼리 구문을 통해 베이스 모델로 `ConvNeXt_Tiny`를 사용할 것을 명시합니다.
> "__OPTIONS__" 쿼리 구문을 통해 모델 생성에 사용할 옵션을 지정합니다. "image_col"은 이미지 경로를 담은 컬럼의 이름이며, "label_col"은 목표값의 정보를 담은 컬럼의 이름입니다. "epochs"를 통해 몇 번의 학습을 반복할 지를 지정합니다(훈련 데이터 세트에 포함된 모든 데이터들이 한 번씩 모델을 통과한 횟수로, 모든 학습 데이터 세트를 학습하는 횟수를 의미합니다). 여기서는 빠르게 학습하기 위해 1로 지정했습니다. 일반적으로 숫자가 클수록 많은 계산시간이 소요되지만 학습이 진행됨에 따라 예측 성능이 올라갑니다. 

<br>

## 3. 만든 모델을 사용하여 강아지/고양이 이미지 분류 결과 예측

이전 단계에서 만든 이미지 분류 예측 모델(`my_image_classifier`)을 사용해서 특정 이미지(학습에 이용되지 않은 데이터 테이블, `cat_and_dog_test`)의 목표값을 예측해 봅니다. 

```python
%%thanosql
PREDICT USING my_image_classifier
OPTIONS (image_col='image')
AS 
SELECT * 
FROM cat_and_dog_test
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>image</th>
      <th>label</th>
      <th>predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>/data/Cat and Dog/test_set/test_set/dogs...</td>
      <td>dog</td>
      <td>dog</td>
    </tr>
    <tr>
      <th>1</th>
      <td>/data/Cat and Dog/test_set/test_set/dogs...</td>
      <td>dog</td>
      <td>dog</td>
    </tr>
    <tr>
      <th>2</th>
      <td>/data/Cat and Dog/test_set/test_set/dogs...</td>
      <td>dog</td>
      <td>dog</td>
    </tr>
    <tr>
      <th>3</th>
      <td>/data/Cat and Dog/test_set/test_set/dogs...</td>
      <td>dog</td>
      <td>dog</td>
    </tr>
    <tr>
      <th>4</th>
      <td>/data/Cat and Dog/test_set/test_set/dogs...</td>
      <td>dog</td>
      <td>dog</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1995</th>
      <td>/data/Cat and Dog/test_set/test_set/cats...</td>
      <td>cat</td>
      <td>cat</td>
    </tr>
    <tr>
      <th>1996</th>
      <td>/data/Cat and Dog/test_set/test_set/cats...</td>
      <td>cat</td>
      <td>cat</td>
    </tr>
    <tr>
      <th>1997</th>
      <td>/data/Cat and Dog/test_set/test_set/cats...</td>
      <td>cat</td>
      <td>cat</td>
    </tr>
    <tr>
      <th>1998</th>
      <td>/data/Cat and Dog/test_set/test_set/cats...</td>
      <td>cat</td>
      <td>cat</td>
    </tr>
    <tr>
      <th>1999</th>
      <td>/data/Cat and Dog/test_set/test_set/cats...</td>
      <td>cat</td>
      <td>cat</td>
    </tr>
  </tbody>
</table>
<p>2000 rows × 3 columns</p>
</div>

> #### 쿼리 세부 정보
> 
> "__PREDICT USING__" 쿼리 구문을 통해 이전 단계에서 만든 `my_image_classifier` 모델을 예측에 사용합니다. "__OPTIONS__"의 파라미터인 "image_col"에는 예측에 사용할 이미지의 경로가 기록되어 있는 컬럼의 이름인 `image`를 지정합니다. 예측 결과는 `predicted` 컬럼에 저장되어 반환됩니다. <br>


> (향후 내용 추가 필요) 
> PRINT를 이용한 이미지 결과 샘플 출력

<br>

## 4. 이미 학습 된 모델을 사용하여 강아지/고양이 이미지 분류 결과 예측

> (향후 내용 추가 필요) 사전에 학습을 오랜 시간동안 해 둔 이미지 예측 모델(`------_imgage_classifier`)을 사용해서 
> 특정 이미지(학습에 이용되지 않은 데이터 테이블, `cat_and_dog_test`)의 목표값을 예측해 봅니다. 

<br>

## 5. 튜토리얼을 마치며

이번 튜토리얼에서는 `Cat and Dog` 데이터 세트를 사용하여 이미지 분류 모델을 만들어 보았습니다. 초급 단계 튜토리얼인만큼 정확도 향상을 위한 과정 설명보다는 작동 위주의 설명으로 진행했습니다. 이미지 분류 모델은 각 플랫폼/서비스에 맞는 정밀한 튜닝을 통해 정확도를 향상 시킬 수 있고 적은 양의 데이터 라벨링만으로도 대부분 만족스러운 결과를 얻을 수 있습니다. 나만의 데이터를 이용해서 베이스 모델을 학습하거나, [자가학습(Self-supervised Learning)](https://en.wikipedia.org/wiki/Self-supervised_learning) 모델 등을 이용해 나의 데이터를 수치화하여 변환한 후 자동화 된 머신러닝(Auto-ML) 기법을 이용한 배포도 가능합니다. 다양한 비정형 데이터(오디오/비디오/텍스트 등)와 수치형 데이터들을 결합하여 나만의 모델을 만들고 경쟁력있는 서비스를 제공해 보세요. <br>  
다음단계인  [중급 이미지 분류 모델 만들기](comingsoon) 튜토리얼에서는 이미지 분류 모델을 더욱 심도있게 다뤄봅니다. 내 서비스를 위한 나만의 이미지 분류 모델 구축방법에 대해 더욱 자세히 알고 싶다면 다음 튜토리얼들을 진행해보세요. <br>
* [나만의 데이터 업로드하기](comingsoon)
* [중급 이미지 분류 모델 만들기](comingsoon) 
* [이미지 변환과 Auto-ML을 이용한 나만의 모델 만들기](comingsoon) 
* [나만의 이미지 분류 모델 배포하기](comingsoon)
  
<br>

---

## 나만의 서비스를 위한 모델배포 관련 문의 <br>
ThanoSQL을 활용해 나만의 모델을 만들거나, 나의 서비스에 적용하는데 어려움이 있다면 언제든 아래로 문의주세요😊

이미지 분류 모델 구축 관련 문의: contact@smartmind.team


