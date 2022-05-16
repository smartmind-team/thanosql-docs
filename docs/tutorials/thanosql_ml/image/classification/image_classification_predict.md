# 이미지 분류

## 예측, 평가

이번에는 전 단계에서 만든 이미지 분류모델을 사용하여 실제로 이미지를 예측해보겠습니다.


```sql
%load_ext thanosql
```


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



# TODO
# TODO
# TODO
# TODO
# TODO

## Evalutate
