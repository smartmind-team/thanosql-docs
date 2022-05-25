# 이미지 분류 모델 만들기

## (ThanoSQL 무작정 따라하기) 고양이와 개를 분류하는 이미지 분류 모델 만들기

**[이전 문서 - Auto-ML을 사용하여 타이타닉 생존자 분류 모델 만들기](https://docs.thanosql.ai/tutorials/thanosql_ml/tabular/classification/automl_classification/)** <br>| **[다음 문서 ]()**

## 시작 전 사전 정보

- 튜토리얼 난이도: ★☆☆☆☆
- 읽는데 걸리는 시간: 10분
- 사용 언어 : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 실행 예제 파일 : 
- 참고문서: [(캐글) Cat and Dog 데이터 세트](https://www.kaggle.com/datasets/tongpython/cat-and-dog), [CONVNEXT_TINY](https://pytorch.org/vision/main/models/generated/torchvision.models.convnext_tiny.html)
- 마지막 수정날짜 : 2022-06-01

<br>

## 튜토리얼 소개
> __분류 작업 이해하기__ <br>
> 분류 작업은 목표값(Target)이 속한 범주(Category 또는 Class)를 예측하기 위해 사용하는 [머신러닝(기계학습/Machine Learning)](https://ko.wikipedia.org/wiki/%EA%B8%B0%EA%B3%84_%ED%95%99%EC%8A%B5)의 한 형태입니다. 예를 들어, 남성 또는 여성을 분류하는 이진분류와 동물의 종(개, 고양이, 토끼 등)을 예측하는 다중 분류 모두 분류 작업에 포함됩니다.

인공지능 모델을 이용해서 이미지를 분류하는 대회([ImageNet](https://en.wikipedia.org/wiki/ImageNet))가 2010년부터 열려왔습니다. 2010년에 우승했던 모델의 분류 성능은 약 72% 입니다. 2015년 [ResNet](https://arxiv.org/abs/1512.03385)이라는 모델은 약 96%의 성능을 기록하면서 특정 영역에서는 인간의 분류 능력(해당 데이터의 경우 약 95%)를 뛰어넘기 시작했습니다. 정확한 이미지 분류를 위해서는 대량의 데이터 세트에 대한 [데이터 라벨링](https://en.wikipedia.org/wiki/Labeled_data) 작업이 필요하지만 사전학습 된 인공지능 모델을 이용해서 추가적인 학습을 진행함으로써 소량의 라벨링 된 데이터 세트만을 이용해서 우수한 성능을 낼 수 있는 방법들이 널리 적용되고 있습니다. ThanoSQL에서는 다양한 사전학습 된 인공지능 모델을 제공하고 있으며, 소량의 데이터 라벨링을 통해서도 쉽게 나만의 이미지 분류 모델을 만들 수 있도록 다양한 기능을 제공하고 있습니다. 일부 사전학습 된 모델은 도메인에 따라 별도의 학습과정이 없이도 키워드 검색이나 유사도 검색 등이 가능합니다. 적절하게 학습 된 이미지 분류 모델은 특성을 정량화하기 힘든 이미지 데이터로부터 원하는 인사이트를 추출하고 다양한 서비스에 활용할 수 있습니다.

__아래는 ThanoSQL 이미지 분류 모델의 활용 및 예시입니다.__
- 이미지 분류 모델을 사용하면 온라인 상품판매 서비스에서 상품 등록을 위해 매번 적절한 범주(카테고리)를 찾는 과정을 반복할 필요가 없습니다. 제품의 사진을 찍어서 ThanoSQL의 데이터베이스에 업로드하게 되면 이미지로부터 적절한 카테고리를 찾아서 자동으로 범주를 분류할 수 있습니다. 사용자는 일부 잘못 분류 된 데이터를 수정하는 작업만을 진행함으로써 이미지 분류에 사용하던 시간을 절약할 수 있습니다. 서비스를 지속하면서 데이터의 양이 많아지게 되면 알고리즘의 성능을 향상시킬 수 있고 성능 저하 여부를 모니터링하고 관리할 수 있습니다. 
- 미술 작품의 대여/판매 서비스를 하는 경우 각 작품의 느낌, 기법, 어울리는 장소 등 사람도 정확하게 분류하기 어려운 부분들을 이미지 분류 모델을 이용해서 대략적으로 분류할 수 있습니다. 해당 작품을 좋아하는 사람들의 행동이력(구매/대여 이력, 선호도 등)을 데이터베이스에 저장하고, 이러한 데이터를 이용해서 해당 작품을 좋아할 것으로 예상되는 그룹을 찾는 것을 정답이 되도록 분류 모델을 만들 수도 있습니다. 즉, 미술 작품의 이미지만을 이용해서 연령대(20대/30대/40대 등), 성별(남/여), 전시 장소(집/카페/회사 등) 등에서의 선호도를 예측하는 모델을 만들 수 있습니다. 
- 제조 공장 등에서 육안으로 확인하던 스크레치나 파손과 같은 불량품을 감지하고 분류 할 수 있습니다. 레이저 스펙트럼과 같은 신호 정보도 시각화 변환 등을 통해서 이미지 분류 모델을 적용할 수 있습니다. 이번 튜토리얼에서는 다루지 않지만 전체 이미지에서 특정 물체(Object)의 위치를 감지하고 경계선(Bounding Box)을 표현할 수 있습니다. 군중 속에서 마스크를 하지 않은 사람을 찾아내거나 자동차 전용 도로에서 보행자나 오토바이 등의 검출이 가능합니다. 

> __본 튜토리얼에서는__ <br>
> 이 예제에서는 캐글의 `Cat and Dog` 데이터 세트를 이용하여 이미지에 고양이가 있는지, 개가 있는 지를 ThanoSQL을 통해 예측하는 작업을 해보겠습니다.

## 의도 & 제한사항

- 이미지 분류 모델은 하나의 이미지에서 하나의 레이블을 예측하는 용도로 사용할 수 있습니다.

- 이미지의 경로를 담은 열과, 이미지의 레이블을 담은 열이 존재해야 합니다.

- 이 모델은 GPU를 사용합니다. 사용한 모델의 크기와 배치 사이즈에 따라 GPU 메모리가 부족할 수 있습니다. 이 경우, 더 작은 모델을 사용하시거나 배치 사이즈를 줄여보십시오.

<br>

## 1. 데이터 세트 확인

이미지 분류 모델을 만들기 전에, 사용할 데이터를 확인해보겠습니다. 캐글의 `Cat and Dog` 데이터는 10000장의 고양이와 개 사진으로 구성되어 있습니다. 데이터는 훈련용 데이터 8000장과 테스트 데이터 2000장으로 나뉘어 ThanoSQL DB에 저장되어 있습니다. 아래 SQL 문법으로 데이터를 확인할 수 있습니다.

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
> 
> `image` 행은 각 이미지의 디렉토리를 가리키며, `label`은 해당 이미지에 포함된 객체가 무엇인지를 가리킵니다.

<br>

## 이미지 분류모델 빌드

이제, thanosql을 사용하여 이미지 분류모델을 빌드합니다.

```sql
%%thanosql
BUILD MODEL my_image_classifier
USING ConvNeXt_Tiny
OPTIONS (image_col='image', label_col='label', epochs=1)
AS SELECT * FROM cat_and_dog_train
```

    <Response [200]>

> #### 쿼리 세부 정보
> 
> BUILD MODEL 구문을 사용하여 `my_image_classifier` 모델을 만듭니다. USING 구문을 통해 기반 모델로 `ConvNeXt_Tiny`를 사용할 것을 명시합니다.
> 
> OPTIONS를 통해 빌드에 사용할 옵션을 지정합니다. `image_col`은 이미지 경로를 담은 행의 이름이며, `label_col`은 레이블 정보를 담은 행입니다. `epochs`를 통해 몇 번의 에포크로 학습할 지를 지정합니다. 여기서는 빠르게 학습하기 위해 1로 지정했습니다.

<br>

## 결과 예측

빌드 완료된 모델을 사용하여, 실제로 이미지를 예측해보겠습니다.

```sql
%%thanosql
PREDICT USING my_image_classifier
OPTIONS (image_col='image')
AS SELECT * FROM cat_and_dog_test
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
> PREDICT USING 쿼리를 통해 이전 단계에서 만든 `my_image_classifier` 모델을 사용할 것이라 지정해주었습니다.
> 
> OPTIONS를 통해 예측에 사용할 옵션을 지정합니다. `image_col`은 이미지의 경로를 담은 행의 이름입니다.
