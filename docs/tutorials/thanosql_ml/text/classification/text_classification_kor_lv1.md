# ThanoSQL 무작정 따라하기

# 영화 리뷰가 긍정적인지 부정적인지를 분류하는 모델 만들기

### 시작 전 사전 정보

- 튜토리얼 난이도: ★☆☆☆☆
- 읽는데 걸리는 시간(실행시간): 10분
- 사용언어: SQL(100%)
- 참고문서: [캐글 IMDB Movies Dataset](https://www.kaggle.com/datasets/harshitshankhdhar/imdb-dataset-of-top-1000-movies-and-tv-shows)

<br>

텍스트 분류란 주어진 텍스트(단어, 문장 또는 문서)를 적절한 클래스로 분류하는 작업입니다. 여기서 클래스는 주어진 데이터 세트에 따라 정해집니다.

### 의도 & 제한사항

- 텍스트 분류 모델은 하나의 텍스트에서 하나의 정답을 예측하는 용도로 사용할 수 있습니다.

- 텍스트를 담은 열과, 레이블을 담은 열이 데이터에 존재해야 합니다.

- 이 모델은 GPU를 사용합니다. 사용한 모델의 크기와 배치 사이즈에 따라 GPU 메모리가 부족할 수 있습니다. 이 경우, 더 작은 모델을 사용하시거나 배치 사이즈를 줄여보십시오.

<br>

## 데이터 확인

텍스트 분류 모델을 만들기 전에, 사용할 데이터를 확인해보겠습니다. 이번 예제에서 사용할 데이터는 `IMDB movie reviews` 데이터로, 영화에 대한 리뷰를 담은 열과 해당 리뷰가 긍정적인지 부정적인지를 담은 레이블이 있는 열로 구성되어 있습니다. ThanoSQL DB에는 해당 데이터의 일부분이 훈련용 데이터와 테스트 데이터로 구분되어 저장되어 있습니다. 아래 SQL문을 통해 데이터를 확인할 수 있습니다.

```sql
%load_ext thanosql
```

```sql
%%thanosql
SELECT * FROM imdb_train LIMIT 5
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
  </tbody>
</table>
</div>

> #### 데이터 이해하기
> 
> `review` 행은 영화 리뷰 텍스트를 담고 있으며, `sentiment`행은 해당 리뷰가 긍정적인지(positive), 부정적인지(negative)를 담고 있습니다.

<br>

## 텍스트 분류모델 빌드

이제, thanosql을 사용하여 이미지 분류모델을 빌드합니다.

```sql
%%thanosql

BUILD MODEL my_movie_review_classifier
USING ElectraEn
OPTIONS (text_col='review', label_col='sentiment', epochs=1, batch_size=4)
AS SELECT * FROM imdb_train
```

    <Response [200]>

> #### 쿼리 세부 정보
> 
> BUILD MODEL 구문을 사용하여 `my_movie_review_classifier` 모델을 만듭니다. USING 구문을 통해 기반 모델로 `ElectraEn`를 사용할 것을 명시합니다.
> 
> OPTIONS를 통해 빌드에 사용할 옵션을 지정합니다. `text_col`은 학습에 사용할 텍스트를 담은 행의 이름이며, `label_col`은 레이블 정보를 담은 행입니다. `epochs`를 통해 몇 번의 에포크로 학습할 지를 지정하고, `batch_size`를 통해 한번에 데이터를 몇 개씩 읽을지 정합니다. 여기서는 빠르게 학습하기 위해 epochs를 1로 지정했습니다.

<br>

## 결과 예측

빌드 완료된 모델을 사용하여, 실제로 영화 리뷰를 예측해보겠습니다.

```sql
%%thanosql
PREDICT USING my_movie_review_classifier
OPTIONS (text_col='review')
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
      <td>negative</td>
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

> #### 쿼리 세부 정보
> 
> PREDICT USING 쿼리를 통해 이전 단계에서 만든 `my_movie_review_classifier` 모델을 사용할 것이라 지정해줍니다.
> 
> OPTIONS를 통해 예측에 사용할 옵션을 지정합니다. `review`는 예측에 사용할 텍스트를 담은 행의 이름입니다.
