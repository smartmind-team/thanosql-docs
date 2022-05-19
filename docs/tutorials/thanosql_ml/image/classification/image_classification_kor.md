# 이미지 분류

이미지 안에 있는 것을 식별하는 작업을 이미지 분류라 합니다. 일반적으로 이미지 분류는 단일 개체가 포함된 이미지를 구분하는 데 사용됩니다.

이 예제에서는 캐글의 Dogs vs. Cats 데이터세트를 이용하여 이미지에 고양이가 있는지, 개가 있는 지를 ThanoSQL을 통해 예측하는 작업을 해보겠습니다.

### 의도 & 제한사항

- 이미지 분류 모델은 하나의 이미지에서 하나의 레이블을 예측하는 용도로 사용할 수 있습니다.

- 이미지의 경로를 담은 열과, 이미지의 레이블을 담은 열이 존재해야 합니다.

- 이 모델은 GPU를 사용합니다. 사용한 모델의 크기와 배치 사이즈에 따라 GPU 메모리가 부족할 수 있습니다. 이 경우, 더 작은 모델을 사용하시거나 배치 사이즈를 줄여보십시오.

<br>

## 이미지 분류모델 빌드

### 데이터

이미지 분류 모델을 만들기 전에, 사용할 데이터를 확인해보겠습니다. 이번 예제에서 사용할 데이터는 캐글의 `Cat and Dog` 데이터로, 10000장의 고양이와 개 사진으로 구성되어 있습니다. 데이터는 훈련용 데이터 8000장과 테스트 데이터 2000장으로 나뉘어 ThanoSQL DB에 저장되어 있습니다. 아래 SQL 문법으로 데이터를 확인할 수 있습니다.


```sql
%load_ext thanosql
```

```sql
%%thanosql
SELECT * FROM cat_and_dog_train
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
      <td>.../Cat and Dog/training_set/training_...</td>
      <td>dog</td>
    </tr>
    <tr>
      <th>1</th>
      <td>.../Cat and Dog/training_set/training_...</td>
      <td>dog</td>
    </tr>
    <tr>
      <th>2</th>
      <td>.../Cat and Dog/training_set/training_...</td>
      <td>dog</td>
    </tr>
    <tr>
      <th>3</th>
      <td>.../Cat and Dog/training_set/training_...</td>
      <td>dog</td>
    </tr>
    <tr>
      <th>4</th>
      <td>.../Cat and Dog/training_set/training_...</td>
      <td>dog</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>7995</th>
      <td>.../Cat and Dog/training_set/training_...</td>
      <td>cat</td>
    </tr>
    <tr>
      <th>7996</th>
      <td>.../Cat and Dog/training_set/training_...</td>
      <td>cat</td>
    </tr>
    <tr>
      <th>7997</th>
      <td>.../Cat and Dog/training_set/training_...</td>
      <td>cat</td>
    </tr>
    <tr>
      <th>7998</th>
      <td>.../Cat and Dog/training_set/training_...</td>
      <td>cat</td>
    </tr>
    <tr>
      <th>7999</th>
      <td>.../Cat and Dog/training_set/training_...</td>
      <td>cat</td>
    </tr>
  </tbody>
</table>
<p>8000 rows × 2 columns</p>
</div>




```sql
%%thanosql
SELECT * FROM cat_and_dog_test
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
      <td>.../Cat and Dog/test_set/test_set/dogs...</td>
      <td>dog</td>
    </tr>
    <tr>
      <th>1</th>
      <td>.../Cat and Dog/test_set/test_set/dogs...</td>
      <td>dog</td>
    </tr>
    <tr>
      <th>2</th>
      <td>.../Cat and Dog/test_set/test_set/dogs...</td>
      <td>dog</td>
    </tr>
    <tr>
      <th>3</th>
      <td>.../Cat and Dog/test_set/test_set/dogs...</td>
      <td>dog</td>
    </tr>
    <tr>
      <th>4</th>
      <td>.../Cat and Dog/test_set/test_set/dogs...</td>
      <td>dog</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1995</th>
      <td>.../Cat and Dog/test_set/test_set/cats...</td>
      <td>cat</td>
    </tr>
    <tr>
      <th>1996</th>
      <td>.../Cat and Dog/test_set/test_set/cats...</td>
      <td>cat</td>
    </tr>
    <tr>
      <th>1997</th>
      <td>.../Cat and Dog/test_set/test_set/cats...</td>
      <td>cat</td>
    </tr>
    <tr>
      <th>1998</th>
      <td>.../Cat and Dog/test_set/test_set/cats...</td>
      <td>cat</td>
    </tr>
    <tr>
      <th>1999</th>
      <td>.../Cat and Dog/test_set/test_set/cats...</td>
      <td>cat</td>
    </tr>
  </tbody>
</table>
<p>2000 rows × 2 columns</p>
</div>



이제, thanosql을 사용하여 이미지 분류모델을 빌드합니다.


```sql
%%thanosql

BUILD MODEL my_image_classifier
USING ConvNeXt_Tiny
OPTIONS (image_col='image', label_col='label', epochs=1)
AS SELECT * FROM cat_and_dog_train
```

<br>

## 예측

이번에는 전 단계에서 만든 이미지 분류모델을 사용하여 실제로 이미지를 예측해보겠습니다.



```sql
%%thanosql
PREDICT USING my_image_classifier
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
      <td>.../Cat and Dog/test_set/test_set/dogs...</td>
      <td>dog</td>
      <td>dog</td>
    </tr>
    <tr>
      <th>1</th>
      <td>.../Cat and Dog/test_set/test_set/dogs...</td>
      <td>dog</td>
      <td>dog</td>
    </tr>
    <tr>
      <th>2</th>
      <td>.../Cat and Dog/test_set/test_set/dogs...</td>
      <td>dog</td>
      <td>dog</td>
    </tr>
    <tr>
      <th>3</th>
      <td>.../Cat and Dog/test_set/test_set/dogs...</td>
      <td>dog</td>
      <td>dog</td>
    </tr>
    <tr>
      <th>4</th>
      <td>.../Cat and Dog/test_set/test_set/dogs...</td>
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
      <td>.../Cat and Dog/test_set/test_set/cats...</td>
      <td>cat</td>
      <td>cat</td>
    </tr>
    <tr>
      <th>1996</th>
      <td>.../Cat and Dog/test_set/test_set/cats...</td>
      <td>cat</td>
      <td>cat</td>
    </tr>
    <tr>
      <th>1997</th>
      <td>.../Cat and Dog/test_set/test_set/cats...</td>
      <td>cat</td>
      <td>cat</td>
    </tr>
    <tr>
      <th>1998</th>
      <td>.../Cat and Dog/test_set/test_set/cats...</td>
      <td>cat</td>
      <td>cat</td>
    </tr>
    <tr>
      <th>1999</th>
      <td>.../Cat and Dog/test_set/test_set/cats...</td>
      <td>cat</td>
      <td>cat</td>
    </tr>
  </tbody>
</table>
<p>2000 rows × 3 columns</p>
</div>



결과를 파이썬 변수로 받고싶다면, 다음처럼 `%%thanosql`이 아니라 `%thanosql`을 사용하면 됩니다.


```sql
result = %thanosql PREDICT USING my_image_classifier AS SELECT * FROM cat_and_dog_test
```


```sql
result
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
      <td>.../Cat and Dog/test_set/test_set/dogs...</td>
      <td>dog</td>
      <td>dog</td>
    </tr>
    <tr>
      <th>1</th>
      <td>.../Cat and Dog/test_set/test_set/dogs...</td>
      <td>dog</td>
      <td>dog</td>
    </tr>
    <tr>
      <th>2</th>
      <td>.../Cat and Dog/test_set/test_set/dogs...</td>
      <td>dog</td>
      <td>dog</td>
    </tr>
    <tr>
      <th>3</th>
      <td>.../Cat and Dog/test_set/test_set/dogs...</td>
      <td>dog</td>
      <td>dog</td>
    </tr>
    <tr>
      <th>4</th>
      <td>.../Cat and Dog/test_set/test_set/dogs...</td>
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
      <td>.../Cat and Dog/test_set/test_set/cats...</td>
      <td>cat</td>
      <td>cat</td>
    </tr>
    <tr>
      <th>1996</th>
      <td>.../Cat and Dog/test_set/test_set/cats...</td>
      <td>cat</td>
      <td>cat</td>
    </tr>
    <tr>
      <th>1997</th>
      <td>.../Cat and Dog/test_set/test_set/cats...</td>
      <td>cat</td>
      <td>cat</td>
    </tr>
    <tr>
      <th>1998</th>
      <td>.../Cat and Dog/test_set/test_set/cats...</td>
      <td>cat</td>
      <td>cat</td>
    </tr>
    <tr>
      <th>1999</th>
      <td>.../Cat and Dog/test_set/test_set/cats...</td>
      <td>cat</td>
      <td>cat</td>
    </tr>
  </tbody>
</table>
<p>2000 rows × 3 columns</p>
</div>

