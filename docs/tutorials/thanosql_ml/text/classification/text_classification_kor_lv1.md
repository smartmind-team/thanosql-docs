# 텍스트 분류 모델 만들기

## (ThanoSQL 무작정 따라하기) 영화 리뷰에서 감정(긍정/부정) 분류 모델 만들기

**[이전 문서 - 고양이와 강아지를 분류하는 이미지 분류 모델 만들기](https://github.com/smartmind-team/thanosql-docs/blob/dowon/docs/tutorials/thanosql_ml/image/classification/image_classification_kor_lv1.md)** <br>| **[다음 문서 - Auto-ML을 사용하여 타이타닉 생존자 분류 모델 만들기](https://docs.thanosql.ai/tutorials/thanosql_ml/tabular/classification/automl_classification/)** 

### 시작 전 사전 정보

- 튜토리얼 난이도: ★☆☆☆☆
- 읽는데 걸리는 시간(실행시간): 10분
- 사용 언어 : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 실행 예제 파일 : 
- 참고문서: [(캐글) IMDB Movie Reviews](https://www.kaggle.com/code/lakshmi25npathi/sentiment-analysis-of-imdb-movie-reviews/data), [ELECTRA: Pre-training Text Encoders as Discriminators Rather Than Generators](https://arxiv.org/abs/2003.10555)
- 마지막 수정날짜 : 2022-06-01

<br>

## 튜토리얼 소개
> __분류 작업 이해하기__ <br>
> 분류 작업은 목표값(Target)이 속한 범주(Category 또는 Class)를 예측하기 위해 사용하는 [머신러닝(기계학습/Machine Learning)](https://ko.wikipedia.org/wiki/%EA%B8%B0%EA%B3%84_%ED%95%99%EC%8A%B5)의 한 형태입니다. 예를 들어, 남성 또는 여성을 분류하는 이진분류와 동물의 종(개, 고양이, 토끼 등)을 예측하는 다중 분류 모두 분류 작업에 포함됩니다.

텍스트 감정 분석 등과 같은 작업을 머신러닝 분야에서는 자연어 처리(NLP, Natural Language Processing)라고 합니다. 자연어 처리 분야는 사람과 사람 간 지식, 의견, 정보 등을 전달하는 소통 수단인 언어를 컴퓨터가 이해할 수 있도록 변환하여 처리하는 분야 전반을 이야기 합니다. 최근 [BERT](https://en.wikipedia.org/wiki/BERT_(language_model)), [GPT-3](https://en.wikipedia.org/wiki/GPT-3) 등 언어 사전 훈련의 발전으로 자연 언어 처리 분야에서 모델의 성능이 크게 향상되었습니다. 이러한 알고리즘들은 많은 양의 목표값(Target)이 없는 텍스트를 활용하여 감정 분석이나 질의 응답과 같은 특정 NLP 작업에 대해 미세 조정하기 전에 언어 이해의 일반적인 모델을 구축한다는 공통점이 있습니다. 즉, 대량의 데이터 세트에 대한 [데이터 라벨링](https://en.wikipedia.org/wiki/Labeled_data) 작업을 최소화 함으로써 더욱 많은 데이터를 효율적으로 활용할 수 있습니다. 하지만 이런 모델들은 학습 시 상당히 많은 계산량을 필요로 하는 단점이 있어, 모델의 정확도와 함께 학습의 효율성에 대한 연구가 함께 이루어지고 있습니다. <br><br>
회사가 고객과의 상담 내용을 텍스트 데이터로 변환한 다음 자연어 처리하여 고객의 만족 및 불만족 사유를 분석하는 것도 하나의 예로 볼 수 있습니다. ThanoSQL에서는 다양한 사전학습 된 인공지능 모델을 제공하고 있으며, 소량의 데이터 라벨링을 통해서도 쉽게 나만의 텍스트 분류 모델을 만들 수 있도록 다양한 기능을 제공하고 있습니다. 일부 사전학습 된 모델은 도메인에 따라 별도의 학습과정이 없이도 키워드 검색이나 유사도 검색 등이 가능합니다. 적절하게 학습 된 텍스트 분류 모델은 특성을 정량화하기 힘든 텍스트 데이터로부터 원하는 인사이트를 추출하고 다양한 서비스에 활용할 수 있습니다.

__아래는 ThanoSQL 텍스트 분류 모델의 활용 및 예시입니다.__
- 고객 상담/문의 데이터의 감정 분석이나 범주 구분에 텍스트 분류 모델을 사용할 수 있습니다. 챗봇, 고객 이메일, 게시판 내의 텍스트를 ThanoSQL DB에 업로드 하게 되면 메세지를 적절한 담당자에게 빠르게 전달이 가능해 집니다. 또한, [ThanoSQL 음성 인식 모델]()을 이용해서 전화 상담 데이터를 텍스트로 변환할 수 있고 이를 이용해서 사용자의 감정 분석이나 상담 트렌드 등을 분석할 수 있습니다. 초보 상담사에게는 사용자의 문의에 대한 답변이 될 수 있는 후보 답변이나 과거의 유사 사례, 필요한 정보 등을 빠르게 검색하고 제공할 수 있습니다. 
- 텍스트 분류 모델을 사용하면 뉴스/게시물 공유 서비스에서 게시 콘텐츠의 그룹을 자동으로 분류해 줄 수 있습니다. 또한 각 콘텐츠의 댓글에 텍스트 분류 모델을 적용 함으로써 감정 분석이 가능하고 갑자기 이슈가 되거나 욕설/비방 등에 의해 발생할 수 있는 문제를 효율적으로 관리할 수 있습니다. 또한, 이메일이나 메신저의 스팸 분류나 텍스트 광고의 필터링에 텍스트 분류 모델을 사용할 수 있습니다. 

> __본 튜토리얼에서는__ <br>
> 대표적인 머신러닝 경진대회 플랫폼인 [캐글](https://www.kaggle.com/)의 `IMDB Movie Reviews` 데이터 세트를 사용하여 영화 리뷰의 감정을 분류하는 모델을 만듭니다. 이 데이터 세트는 50,000 개의 영화 리뷰 텍스트와 긍정/부정 감정에 대한 목표값(Target)으로 구성되어 있습니다. 영화 평점을 기준으로 5보다 작은 값을 부정, 7보다 큰 값을 긍정으로 표현하였으며 각 개별 영화는 30 개 이상의 리뷰 결과를 갖지 않습니다. 25,000 개의 학습용 데이터와 25,000 개의 테스트용 데이터로 구분되어 있으며 두 그룹 간에 중복되는 영화는 없습니다. <br>

> __튜토리얼의 의도 및 제한사항__
> - 텍스트 분류 모델은 하나의 텍스트에서 하나의 목표값(Target, 범주/레이블/라벨)를 예측하는 용도로 사용할 수 있습니다.
> - 텍스트를 나타내는 컬럼(Column)과, 텍스트의 목표값을 나타내는 컬럼이 존재해야 합니다.
> - 해당 텍스트 분류 모델의 베이스 모델(`ELECTRA`)은 GPU를 사용합니다. 사용한 모델의 크기와 배치 사이즈에 따라 GPU 메모리가 부족할 수 있습니다. 이 경우, 더 작은 모델을 사용하시거나 배치 사이즈를 줄여보십시오.
<br>

## 1. 데이터 세트 확인

본 튜토리얼을 진행하기 위해 우리는 ThanoSQL DB에 저장되어 있는 `imdb_train` 테이블을 사용합니다. 아래의 쿼리문을 실행하여 테이블 내용을 확인합니다.

```python
%%thanosql
SELECT * 
FROM imdb_train 
LIMIT 5
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
> - `review` : 영화 리뷰 텍스트
> - `sentiment` : 해당 리뷰가 긍정적인지(positive), 부정적인지(negative)를 나타내는 목표값(Target)

<br>

## 2. 텍스트 분류모델 만들기

이전 단계에서 확인한 `imdb_train` 데이터 세트를 사용하여 텍스트 분류 모델을 만듭니다. 아래의 쿼리 구문을 실행하여 `my_movie_review_classifier`라는 이름의 모델을 만듭니다.

```sql
%%thanosql

BUILD MODEL my_movie_review_classifier
USING ElectraEn
OPTIONS (
  text_col='review', 
  label_col='sentiment', 
  epochs=1, 
  batch_size=4
  )
AS 
SELECT * 
FROM imdb_train
```

    <Response [200]>

> #### 쿼리 세부 정보 <br>
> "__BUILD MODEL__" 쿼리 구문을 사용하여 `my_movie_review_classifier` 모델을 만들고 학습시킵니다. "__USING__" 쿼리 구문을 통해 베이스 모델로 `ElectraEn`을 사용할 것을 명시합니다. "__OPTIONS__" 쿼리 구문을 통해 모델 생성에 사용할 옵션을 지정합니다. "text_col"은 학습에 사용할 텍스트를 담은 컬럼의 이름이며, "label_col"은 목표값의 정보를 담은 컬럼의 이름입니다. "epochs"를 통해 몇 번의 학습을 반복할 지를 지정합니다(훈련 데이터 세트에 포함된 모든 데이터들이 한 번씩 모델을 통과한 횟수로, 모든 학습 데이터 세트를 학습하는 횟수를 의미합니다). 여기서는 빠르게 학습하기 위해 1로 지정했습니다. 일반적으로 숫자가 클수록 많은 계산시간이 소요되지만 학습이 진행됨에 따라 예측 성능이 올라갑니다. "batch_size"는 한 번의 학습에서 읽는 데이터세트 묶음의 크기입니다. 클수록 빠르게 학습이 이루어 질 수 있지만 많은 양의 메모리를 사용하게 되기 때문에 큰 값을 사용하는 경우 GPU 메모리가 부족할 수 있습니다.

<br>

## 3. 만든 모델을 사용하여 영화 리뷰 감정 분류 결과 예측

이전 단계에서 만든 텍스트 분류 예측 모델(`my_movie_review_classifier`)을 사용해서 특정 리뷰(학습에 이용되지 않은 데이터 테이블, `imdb_test`)의 목표값(감정)을 예측해 봅니다.

```python
%%thanosql
PREDICT USING my_movie_review_classifier
OPTIONS (text_col='review')
AS 
SELECT * 
FROM imdb_test
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
> "__PREDICT USING__" 쿼리 구문을 통해 이전 단계에서 만든 `my_movie_review_classifier` 모델을 예측에 사용합니다.
> 
> OPTIONS를 통해 예측에 사용할 옵션을 지정합니다. `review`는 예측에 사용할 텍스트를 담은 행의 이름입니다.

"__PREDICT USING__" 쿼리 구문을 통해 이전 단계에서 만든 my_image_classifier 모델을 예측에 사용합니다. "__OPTIONS__"의 파라미터인 "text_col"에는 예측에 사용할 텍스트(리뷰)가 기록되어 있는 컬럼의 이름인 review를 지정합니다. 예측 결과는 predicted 컬럼에 저장되어 반환됩니다.


## 4. 이미 학습 된 모델을 사용하여 영화 리뷰 감정 분류 결과 예측

> (향후 내용 추가 필요) 사전에 학습을 오랜 시간동안 해 둔 감정 예측 모델(`------_classifier`)을 사용해서 특정 리뷰(학습에 이용되지 않은 데이터 테이블, `imdb_test`)의 목표값(감정)을 예측해 봅니다. <br>


## 5. 튜토리얼을 마치며

이번 튜토리얼에서는 `IMDB Movie Reviews` 데이터 세트를 사용하여 텍스트 분류 모델을 만들어 보았습니다. 초급 단계 튜토리얼인만큼 정확도 향상을 위한 과정 설명보다는 작동 위주의 설명으로 진행했습니다. 텍스트 분류 모델은 각 플랫폼/서비스에 맞는 정밀한 튜닝을 통해 정확도를 향상 시킬 수 있고 적은 양의 데이터 라벨링만으로도 대부분 만족스러운 결과를 얻을 수 있습니다. 나만의 데이터를 이용해서 베이스 모델을 학습하거나, [자가학습(Self-supervised Learning)](https://en.wikipedia.org/wiki/Self-supervised_learning) 모델 등을 이용해 나의 데이터를 수치화하여 변환한 후 자동화 된 머신러닝(Auto-ML) 기법을 이용한 배포도 가능합니다. 다양한 비정형 데이터(이미지/오디오/비디오 등)와 수치형 데이터들을 결합하여 나만의 모델을 만들고 경쟁력있는 서비스를 제공해 보세요. <br>  
다음단계인 [중급 텍스트 분류 모델 만들기](comingsoon) 튜토리얼에서는 텍스트 분류 모델을 더욱 심도있게 다뤄봅니다. 내 서비스를 위한 나만의 텍스트 분류 모델 구축방법에 대해 더욱 자세히 알고 싶다면 다음 튜토리얼들을 진행해보세요. <br>
* [나만의 데이터 업로드하기](comingsoon)
* [중급 텍스트 분류 모델 만들기](comingsoon) 
* [텍스트 변환과 Auto-ML을 이용한 나만의 모델 만들기](comingsoon) 
* [나만의 텍스트 분류 모델 배포하기](comingsoon)

<br>

---

## 나만의 서비스를 위한 모델배포 관련 문의 <br>
ThanoSQL을 활용해 나만의 모델을 만들거나, 나의 서비스에 적용하는데 어려움이 있다면 언제든 아래로 문의주세요😊

이미지 분류 모델 구축 관련 문의: contact@smartmind.team
