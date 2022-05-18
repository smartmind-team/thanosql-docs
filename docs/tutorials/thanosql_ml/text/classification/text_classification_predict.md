# 이미지 분류

## 예측

이번에는 전 단계에서 만든 텍스트 분류모델을 사용하여 실제로 영화 리뷰가 긍정인지 부정인지를 예측해보겠습니다.


```sql
%load_ext thanosql
```


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

