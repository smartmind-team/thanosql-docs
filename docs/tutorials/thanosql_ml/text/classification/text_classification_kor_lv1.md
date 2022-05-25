# 텍스트 분류 모델 만들기

## (ThanoSQL 무작정 따라하기) 영화 리뷰에서 감정(긍정/부정) 분류 모델 만들기

**[이전 문서 - 고양이와 강아지를 분류하는 이미지 분류 모델 만들기](https://github.com/smartmind-team/thanosql-docs/blob/dowon/docs/tutorials/thanosql_ml/image/classification/image_classification_kor_lv1.md)** <br>| **[다음 문서 - Auto-ML을 사용하여 타이타닉 생존자 분류 모델 만들기](https://docs.thanosql.ai/tutorials/thanosql_ml/tabular/classification/automl_classification/)** 

### 시작 전 사전 정보

- 튜토리얼 난이도: ★☆☆☆☆
- 읽는데 걸리는 시간(실행시간): 10분
- 사용 언어 : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 실행 예제 파일 : 
- 참고문서: [(캐글) IMDB Movies Dataset](https://www.kaggle.com/datasets/harshitshankhdhar/imdb-dataset-of-top-1000-movies-and-tv-shows), [ELECTRA: Pre-training Text Encoders as Discriminators Rather Than Generators](https://arxiv.org/abs/2003.10555)
- 마지막 수정날짜 : 2022-06-01

<br>

## 튜토리얼 소개
> __분류 작업 이해하기__ <br>
> 분류 작업은 목표값(Target)이 속한 범주(Category 또는 Class)를 예측하기 위해 사용하는 [머신러닝(기계학습/Machine Learning)](https://ko.wikipedia.org/wiki/%EA%B8%B0%EA%B3%84_%ED%95%99%EC%8A%B5)의 한 형태입니다. 예를 들어, 남성 또는 여성을 분류하는 이진분류와 동물의 종(개, 고양이, 토끼 등)을 예측하는 다중 분류 모두 분류 작업에 포함됩니다.

텍스트 감정 분석 등과 같은 작업을 머신러닝 분야에서는 자연어 처리(NLP, Natural Language Processing)라고 합니다. 자연어 처리 분야는 사람과 사람 간 지식, 의견, 정보 등을 전달하는 소통 수단인 언어를 컴퓨터가 이해할 수 있도록 변환하여 처리하는 분야 전반을 이야기 합니다. 최근 BERT, GPT-3 등 언어 사전 훈련의 발전으로 자연 언어 처리 분야에서 모델의 성능이 크게 향상되었습니다. 이러한 알고리즘들은 많은 양의 목표값(Target)이 없는 텍스트를 활용하여 감정 분석이나 질의 응답과 같은 특정 NLP 작업에 대해 미세 조정하기 전에 언어 이해의 일반적인 모델을 구축한다는 공통점이 있습니다. 즉, 대량의 데이터 세트에 대한 [데이터 라벨링](https://en.wikipedia.org/wiki/Labeled_data) 작업을 최소화 함으로써 더욱 많은 데이터를 효율적으로 활용할 수 있습니다. 하지만 이런 모델들은 학습 시 상당히 많은 계산량을 필요로 하는 단점이 있어, 모델의 정확도와 함께 학습의 효율성에 대한 연구가 함께 이루어지고 있습니다. <br><br>
회사가 고객과의 상담 내용을 텍스트 데이터로 변환한 다음 자연어 처리하여 고객의 만족 및 불만족 사유를 분석하는 것도 하나의 예로 볼 수 있습니다. ThanoSQL에서는 다양한 사전학습 된 인공지능 모델을 제공하고 있으며, 소량의 데이터 라벨링을 통해서도 쉽게 나만의 텍스트 분류 모델을 만들 수 있도록 다양한 기능을 제공하고 있습니다. 일부 사전학습 된 모델은 도메인에 따라 별도의 학습과정이 없이도 키워드 검색이나 유사도 검색 등이 가능합니다. 적절하게 학습 된 텍스트 분류 모델은 특성을 정량화하기 힘든 텍스트 데이터로부터 원하는 인사이트를 추출하고 다양한 서비스에 활용할 수 있습니다.

__아래는 ThanoSQL 텍스트 분류 모델의 활용 및 예시입니다.__
- 고객 상담/문의 데이터의 감정 분석이나 범주 구분에 텍스트 분류 모델을 사용할 수 있습니다. 챗봇, 고객 이메일, 게시판 내의 텍스트를 ThanoSQL DB에 업로드 하게 되면 메세지를 적절한 담당자에게 빠르게 전달이 가능해 집니다. 또한, [ThanoSQL 음성 인식 모델]()을 이용해서 전화 상담 데이터를 텍스트로 변환할 수 있고 이를 이용해서 사용자의 감정 분석이나 상담 트렌드 등을 분석할 수 있습니다. 초보 상담사에게는 사용자의 문의에 대한 답변이 될 수 있는 후보 답변이나 과거의 유사 사례, 필요한 정보 등을 빠르게 검색하고 제공할 수 있습니다. 
- 텍스트 분류 모델을 사용하면 뉴스/게시물 공유 서비스에서 게시 콘텐츠의 그룹을 자동으로 분류해 줄 수 있습니다. 또한 각 콘텐츠의 댓글에 텍스트 분류 모델을 적용 함으로써 감정 분석이 가능하고 과하게 이슈가 되거나 욕설/비방 등에 의한 문제를 효율적으로 관리할 수 있습니다. 또한, 이메일이나 메신저의 스팸 분류나 텍스트 광고의 필터링에 텍스트 분류 모델을 사용할 수 있습니다. 

> __본 튜토리얼에서는__ <br>
> 대표적인 머신러닝 경진대회 플랫폼인 캐글의 입문자를 위한 캐글의 Cat and Dog 데이터 세트를 사용하여 고양이와 강아지를 분류하는 모델을 만듭니다. 이 데이터 세트는 25,000장의 훈련 데이터와 12,500장의 테스트 데이터로 구성되어 있습니다. 직관적으로 이해하기 쉽고 각 이미지의 크기가 크지 않아 이미지 분류 모델의 정확도를 판단하기 위해 널리 사용됩니다. 본 튜토리얼에서는 ThanoSQL의 사용방법을 익히고 빠른 결과 확인을 위해, 훈련용 데이터 8,000장과 테스트 데이터 2,000장만을 사용합니다. <br>

> __튜토리얼의 의도 및 제한사항__
> - 텍스트 분류 모델은 하나의 텍스트에서 하나의 정답을 예측하는 용도로 사용할 수 있습니다.
> - 텍스트를 담은 열과, 레이블을 담은 열이 데이터에 존재해야 합니다.
> - 이 모델은 GPU를 사용합니다. 사용한 모델의 크기와 배치 사이즈에 따라 GPU 메모리가 부족할 수 있습니다. 이 경우, 더 작은 모델을 사용하시거나 배치 사이즈를 줄여보십시오.
<br>

## 1. 데이터 세트 확인

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


이미지 캡셔닝, 이미지-텍스트 
