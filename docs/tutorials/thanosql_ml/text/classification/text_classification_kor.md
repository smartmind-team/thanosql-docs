# 텍스트 분류

텍스트 분류란 주어진 텍스트(단어, 문장 또는 문서)를 적절한 클래스로 분류하는 작업입니다. 여기서 클래스는 주어진 데이터셋에 따라 정해집니다.

### 의도 & 제한사항

- 텍스트 분류 모델은 하나의 텍스트에서 하나의 정답을 예측하는 용도로 사용할 수 있습니다.

- 텍스트를 담은 열과, 레이블을 담은 열이 데이터에 존재해야 합니다.

- 이 모델은 GPU를 사용합니다. 사용한 모델의 크기와 배치 사이즈에 따라 GPU 메모리가 부족할 수 있습니다. 이 경우, 더 작은 모델을 사용하시거나 배치 사이즈를 줄여보십시오.

<br>

## 텍스트 분류모델 빌드

### 데이터

모델을 빌드하기 전에, 사용할 데이터를 확인해보겠습니다. 이번 예제에서 사용할 데이터는 5만 건의 영화 리뷰 정보를 담고 있는 IMDB 데이터입니다. 우리는 그 중 5000개만을 골라, 4000개는 훈련 데이터로, 나머지 1000개는 테스트 데이터로 사용할 예정입니다. SQL 문법으로 데이터를 확인할 수 있습니다.


```sql
%load_ext thanosql
```


```sql
%%thanosql
SELECT * FROM imdb_train
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>review</th>
      <th>sentiment</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>This is the kind of movie that BEGS to be show...</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Bulletproof is quite clearly a disposable film...</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>2</th>
      <td>A beautiful shopgirl in London is swept off he...</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>3</th>
      <td>VERY dull, obvious, tedious Exorcist rip-off f...</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Do we really need any more narcissistic garbag...</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>3995</th>
      <td>During my trip in a youth leadership forum, I ...</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>3996</th>
      <td>Stilted, stagy, strange and opaque, if visuall...</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>3997</th>
      <td>This movie was great! It was an excellent rend...</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>3998</th>
      <td>It pains me to see an awesome movie turn into ...</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>3999</th>
      <td>Notable because of it's notorious explicit sce...</td>
      <td>positive</td>
    </tr>
  </tbody>
</table>
<p>4000 rows × 2 columns</p>
</div>


```sql
%%thanosql
SELECT * FROM imdb_test
```


<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>review</th>
      <th>sentiment</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I read the book before seeing the movie, and t...</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>1</th>
      <td>"9/11," hosted by Robert DeNiro, presents foot...</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Yesterday I attended the world premiere of "De...</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Moonwalker is a Fantasy Music film staring Mic...</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Welcome to Oakland, where the dead come out to...</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>995</th>
      <td>I remember catching this movie on one of the S...</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>996</th>
      <td>CyberTracker is set in Los Angeles sometime in...</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>997</th>
      <td>There is so much that is wrong with this film,...</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>998</th>
      <td>I am a firm believer that a film, TV serial or...</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>999</th>
      <td>I think vampire movies (usually) are wicked. E...</td>
      <td>negative</td>
    </tr>
  </tbody>
</table>
<p>1000 rows × 2 columns</p>
</div>


이제, thanosql을 사용하여 이미지 분류모델을 빌드합니다.

```sql
%%thanosql

BUILD MODEL my_movie_review_classifier
USING ElectraEn
OPTIONS (text_col='review', label_col='sentiment', epochs=1, batch_size=8)
AS SELECT * FROM imdb_train
```

<br>

## 예측

이번에는 전 단계에서 만든 텍스트 분류모델을 사용하여 실제로 영화 리뷰가 긍정인지 부정인지를 예측해보겠습니다.


```sql
%%thanosql
PREDICT USING my_movie_review_classifier
OPTIONS (text_col="review")
AS SELECT * FROM imdb_test
```


<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>review</th>
      <th>sentiment</th>
      <th>predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I read the book before seeing the movie, and t...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>1</th>
      <td>"9/11," hosted by Robert DeNiro, presents foot...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Yesterday I attended the world premiere of "De...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Moonwalker is a Fantasy Music film staring Mic...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Welcome to Oakland, where the dead come out to...</td>
      <td>positive</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>995</th>
      <td>I remember catching this movie on one of the S...</td>
      <td>negative</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>996</th>
      <td>CyberTracker is set in Los Angeles sometime in...</td>
      <td>negative</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>997</th>
      <td>There is so much that is wrong with this film,...</td>
      <td>negative</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>998</th>
      <td>I am a firm believer that a film, TV serial or...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>999</th>
      <td>I think vampire movies (usually) are wicked. E...</td>
      <td>negative</td>
      <td>negative</td>
    </tr>
  </tbody>
</table>
<p>1000 rows × 3 columns</p>
</div>



결과를 파이썬 변수로 받고싶다면, 다음처럼 `%%thanosql`이 아니라 `%thanosql`을 사용하면 됩니다.


```sql
result = %thanosql PREDICT USING my_movie_review_classifier OPTIONS (text_col="review") AS SELECT * FROM imdb_test
```


```sql
result
```




<div>
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>review</th>
      <th>sentiment</th>
      <th>predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I read the book before seeing the movie, and t...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>1</th>
      <td>"9/11," hosted by Robert DeNiro, presents foot...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Yesterday I attended the world premiere of "De...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Moonwalker is a Fantasy Music film staring Mic...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Welcome to Oakland, where the dead come out to...</td>
      <td>positive</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>995</th>
      <td>I remember catching this movie on one of the S...</td>
      <td>negative</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>996</th>
      <td>CyberTracker is set in Los Angeles sometime in...</td>
      <td>negative</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>997</th>
      <td>There is so much that is wrong with this film,...</td>
      <td>negative</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>998</th>
      <td>I am a firm believer that a film, TV serial or...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>999</th>
      <td>I think vampire movies (usually) are wicked. E...</td>
      <td>negative</td>
      <td>negative</td>
    </tr>
  </tbody>
</table>
<p>1000 rows × 3 columns</p>
</div>

