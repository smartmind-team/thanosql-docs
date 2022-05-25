# 이미지 분류 모델 만들기

## (ThanoSQL 무작정 따라하기) 고양이와 개를 분류하는 이미지 분류 모델 만들기

### 시작 전 사전 정보

- 튜토리얼 난이도: ★☆☆☆☆
- 읽는데 걸리는 시간: 10분
- 사용 언어 : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 실행 예제 파일 : 
- 참고문서: [캐글 Cat and Dog 데이터 세트](https://www.kaggle.com/datasets/tongpython/cat-and-dog), [CONVNEXT_TINY](https://pytorch.org/vision/main/models/generated/torchvision.models.convnext_tiny.html)
- 마지막 수정날짜 : 2022-06-01

<br>

### 튜토리얼 소개

이미지 분류는 주어진 이미지 안에 목표로 하는 어느 특정한 클래스에 해당하는 물체가 있는지 여부를 분류하는 작업입니다. 일반적으로 이미지 분류는 단일 개체가 포함된 이미지를 구분하는 데 사용됩니다.

이 예제에서는 캐글의 Cat and Dog 데이터 세트를 이용하여 이미지에 고양이가 있는지, 개가 있는 지를 ThanoSQL을 통해 예측하는 작업을 해보겠습니다.

### 의도 & 제한사항

- 이미지 분류 모델은 하나의 이미지에서 하나의 레이블을 예측하는 용도로 사용할 수 있습니다.

- 이미지의 경로를 담은 열과, 이미지의 레이블을 담은 열이 존재해야 합니다.

- 이 모델은 GPU를 사용합니다. 사용한 모델의 크기와 배치 사이즈에 따라 GPU 메모리가 부족할 수 있습니다. 이 경우, 더 작은 모델을 사용하시거나 배치 사이즈를 줄여보십시오.

<br>

## 데이터 확인

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
