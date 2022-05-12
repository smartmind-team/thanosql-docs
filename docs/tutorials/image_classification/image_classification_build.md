# 이미지 분류

## 이미지 분류모델 빌드

### 데이터

모델을 빌드하기 전에, 사용할 데이터를 확인해보겠습니다. 이번 예제에서 사용할 데이터는 캐글의 Cat and Dog 데이터로, 10000장의 고양이와 개 사진으로 구성되어 있습니다. 그 중 8000장은 훈련 데이터로, 2000장은 테스트 데이터로 사용할 예정입니다. SQL 문법으로 데이터를 확인할 수 있습니다.


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
