---
title: 텍스트로 텍스트 검색하기
---

# __텍스트로 텍스트 검색하기__

- 튜토리얼 난이도: ★★☆☆☆
- 읽는데 걸리는 시간: 7분
- 사용 언어: [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 실행 파일 위치: tutorial/thanosql_search/search_text_by_text.ipynb   
- 참고 문서: [Naver sentiment movie corpus v1.0](https://github.com/e9t/nsmc#naver-sentiment-movie-corpus-v10), [단어 임베딩: 어휘의 의미(LEXICAL SEMANTICS)를 인코딩하기](https://tutorials.pytorch.kr/beginner/nlp/word_embeddings_tutorial.html), [컴퓨터가 바라보는 문자](https://heung-bae-lee.github.io/2020/01/16/NLP_01/)

## 튜토리얼 소개

<div class="admonition note">
    <h4 class="admonition-title">텍스트 수치화 기술 이해하기</h4>
    <p>컴퓨터는 사람이 쓰는 언어(자연어)를 곧바로 입력으로 받을 수 없습니다. 때문에 자연어를 기계가 인식할 수 있는 수치 데이터로 변환하는 과정이 필요합니다. 자연어 처리분야에서 임베딩이란 이렇게 사람이 쓰는 자연어를 기계가 이해할 수 있는 숫자형태인 vector로 바꾼 결과 혹은 그 일련의 과정 전체를 의미합니다.</p>
</div>

자연어의 임베딩을 구하는 기법은 크게 통계적인 기법과, 인공신경망 기반 기법으로 구분되고, 세부적으로도 더 나눌 수 있습니다. ThanoSQL에서는 인공신경망 기반의 자가학습모델을 사용하여, 텍스트를 잘 표현하는 벡터를 스스로 학습하는 방법을 제공합니다.

<div class="admonition note">
    <h4 class="admonition-title">본 튜토리얼에서는</h4>
    <p>👉 이번 튜토리얼에서는 <mark style="background-color:#FFD79C">영화 리뷰 데이터</mark>를 사용합니다. 데이터는 영화 리뷰 텍스트와, 라벨값으로 구성되어있지만, 자가학습법을 사용해 훈련하는 예제를 보여드리기 위해 라벨값은 사용하지 않습니다. 10000개의 영화 리뷰 데이터를 학습하여, 입력 문장과 유사한 문장 찾기, 문장의 키워드 추출하기를 해보겠습니다.</p>
</div>

## __0. 데이터 세트 준비__

ThanoSQL의 쿼리 구문을 사용하기 위해서는 [ThanoSQL 워크스페이스](https://docs.thanosql.ai/ko/getting_started/how_to_use_ThanoSQL/#5-thanosql)
에서 언급된 것처럼 API 토큰을 생성하고 아래의 쿼리를 실행해야 합니다.


```python
%load_ext thanosql
%thanosql API_TOKEN=<발급받은_API_TOKEN>
```

### __데이터 세트 준비__


```python
%%thanosql
GET THANOSQL DATASET nsmc_data
OPTIONS (overwrite=True)
```

    Success


<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>GET THANOSQL DATASET</strong>" 쿼리 구문을 사용하여 원하는 데이터 세트를 워크스페이스에 저장합니다. </li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문을 통해 <strong>GET THANOSQL DATASET</strong> 에 사용할 옵션을 지정합니다.
        <ul>
            <li>"overwrite": 동일 이름의 데이터 세트가 존재하는 경우 덮어쓰기 가능 여부 설정. True일 경우 기존 데이터 세트는 새로운 데이터 세트로 변경됨 (True|False, default: False) </li>
        </ul>
        </li>
    </ul>
</div>


```python
%%thanosql
COPY nsmc_train
OPTIONS (overwrite=True)
FROM "thanosql-dataset/nsmc_data/nsmc_sample_train.csv"
```

    Success



```python
%%thanosql
COPY nsmc_test
OPTIONS (overwrite=True)
FROM "thanosql-dataset/nsmc_data/nsmc_sample_test.csv"
```

    Success


<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>COPY</strong>" 쿼리 구문을 사용하여 데이터베이스에 저장 할 데이터 세트명을 지정합니다. </li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문을 통해 <strong>COPY</strong> 에 사용할 옵션을 지정합니다.
        <ul>
            <li>"overwrite": 동일 이름의 데이터 세트가 데이터베이스 상에 존재하는 경우 덮어쓰기 가능 여부 설정. True일 경우 기존 데이터 세트는 새로운 데이터 세트로 변경됨 (True|False, default: False) </li>
        </ul>
        </li>
    </ul>
</div>

## __1. 데이터 세트 확인__

텍스트 수치화 모델을 만들기 위해 ThanoSQL 워크스페이스 데이터베이스에 저장되어 있는 <mark style="background-color:#FFEC92">nsmc_train</mark> 테이블을 사용합니다. <mark style="background-color:#FFEC92">nsmc_train</mark> 테이블은 <mark style="background-color:#FFD79C">NAVER Sentiment Movie Corpus</mark> 영화 리뷰 데이터 및 라벨 정보의 일부가 담겨 있는 테이블입니다. 아래의 쿼리문을 실행하고 테이블의 내용을 확인합니다.


```python
%%thanosql
SELECT *
FROM nsmc_train
LIMIT 5
```




<div class="df_size">
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>document</th>
      <th>label</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>9465891</td>
      <td>저퀄리티. 뭐하나 장점이 없음.</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>9792014</td>
      <td>10점 만점에 1점 !!! 최악이네 하아...</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3795348</td>
      <td>누가 억지눈물 잘흘리나 자기네들끼리 대결하는 작품</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1129393</td>
      <td>신도 싫고 인간도 싫다</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4678610</td>
      <td>추억의 명작만화</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">데이터 테이블 이해하기</h4>
    <p><mark style="background-color:#FFEC92">nsmc_train</mark> 테이블은 아래와 같은 정보를 담고 있습니다.</p>
    <ul>
        <li><mark style="background-color:#D7D0FF">id</mark>: 데이터의 식별 번호</li>
        <li><mark style="background-color:#D7D0FF">document</mark>: 영화 리뷰 데이터</li>
        <li><mark style="background-color:#D7D0FF">label</mark>: 라벨값</li>
    </ul>
</div>

## __2. 텍스트 수치화 모델 생성__

이전 단계에서 확인한 <mark style="background-color:#FFEC92">nsmc_train</mark> 테이블을 사용하여 텍스트 수치화 모델을 만듭니다. 아래의 쿼리 구문을 실행하여 <mark style="background-color:#E9D7FD">nsmc_text_search_model</mark>이라는 이름의 모델을 만듭니다.  
(쿼리 실행 시 예상 소요 시간: 2 min)


```python
%%thanosql
BUILD MODEL nsmc_text_search_model
USING SBERTKo
OPTIONS (
    text_col="document",
    overwrite=True
    )
AS
SELECT *
FROM nsmc_train
```

    Success


<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>BUILD MODEL</strong>" 쿼리 구문을 사용하여 <mark style="background-color:#E9D7FD">nsmc_text_search_model</mark> 이라는 모델을 만들고 학습시킵니다.</li>
        <li>"<strong>USING</strong>" 쿼리 구문을 통해 베이스 모델로 <mark style="background-color:#E9D7FD">SBERTKo</mark> 모델을 사용할 것을 명시합니다.</li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문을 통해 모델 생성에 사용할 옵션을 지정합니다.
        <ul>
            <li>"text_col": 데이터 테이블에서 영화 리뷰 데이터를 담은 컬럼 (default: "text")</li>
            <li>"epochs": 텍스트 수치화 모델을 생성하기 위한 데이터 세트 학습 횟수 (int, default: 1)</li>
            <li>"batch_size": 한 번의 예측에서 읽는 데이터 세트 묶음의 크기 (int, default: 16)</li> 
            <li>"learning_rate": 모델의 학습률 (float, default: 3e-5)</li> 
            <li>"train": False일 경우 사전훈련된 모델을 추가로 학습하지 않고 그대로 사용 (True|False, default: True)</li> 
            <li>"overwrite": 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 여부 설정. True일 경우 기존 모델은 새로운 모델로 변경됨 (True|False, default: False)</li>
        </ul>
        </li>
    </ul>
</div>

다음 "__CONVERT USING__ " 쿼리 구문을 실행하여 `nsmc_test` 텍스트 데이터들을 수치화 합니다. 수치화 된 결과는 `nsmc_test` 테이블에 사용자가 옵션으로 지정한 이름(default: <mark style="background-color:#D7D0FF ">convert_result</mark>)의 컬럼에 저장됩니다.


```python
%%thanosql
CONVERT USING nsmc_text_search_model
OPTIONS (
    text_col="document",
    table_name="nsmc_test",
    batch_size=32,
    column_name="convert_result"
    )
AS 
SELECT *
FROM nsmc_test
```




<div class="df_size">
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>document</th>
      <th>label</th>
      <th>convert_result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>9181084</td>
      <td>꼭 한번 봐야하는 영화인 것 같네요 ㅎㅎ</td>
      <td>1</td>
      <td>[0.0037510623, -0.0031921342, -0.01413603, -0....</td>
    </tr>
    <tr>
      <th>1</th>
      <td>6318649</td>
      <td>권선징악은안드로메다로~럽라인은작가빙의된니끼한조연과~여주는남자갖고논질나쁜여자</td>
      <td>0</td>
      <td>[-0.061903417, -0.016521271, 0.007930033, -0.0...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>5718332</td>
      <td>유쾌하고 신나는 스피드 코미디.</td>
      <td>1</td>
      <td>[-0.006741981, -0.05358216, 0.04350551, -0.006...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>8063675</td>
      <td>정말 따뜻했던 드라마ㅠㅠ</td>
      <td>1</td>
      <td>[0.030807665, -0.00572762, 0.012884197, -0.027...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>10229366</td>
      <td>솔직히 배우들의 연기는 나름 괜찮았다 하지만 냉정하게 영화자체만을 놓고보면 별다른 ...</td>
      <td>0</td>
      <td>[-0.002742508, -0.020427909, 0.03971886, -0.01...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1995</th>
      <td>5382227</td>
      <td>30년간 본 수천편의 드라마중 최고는 이 작품</td>
      <td>1</td>
      <td>[-0.018247725, -0.01442731, 0.015619117, -0.03...</td>
    </tr>
    <tr>
      <th>1996</th>
      <td>4584773</td>
      <td>빛돌을 이길 5명의 용사들의 패배</td>
      <td>1</td>
      <td>[-0.042145733, -0.0331033, 0.012950524, 0.0350...</td>
    </tr>
    <tr>
      <th>1997</th>
      <td>2559484</td>
      <td>빌머레이의 굳은 표정에 모든것이 살아있다.. 짐자무시 최고!</td>
      <td>1</td>
      <td>[-0.030894795, 0.0091382265, -0.032140985, 0.0...</td>
    </tr>
    <tr>
      <th>1998</th>
      <td>9867081</td>
      <td>이런영화에 투자하지 않는것이 진정한 한국영화의 발전을위한 초석</td>
      <td>0</td>
      <td>[0.008489536, 0.037255745, 0.042381987, 0.0331...</td>
    </tr>
    <tr>
      <th>1999</th>
      <td>5984181</td>
      <td>처음부터 끝까지 제가 3번은 더 돌려봤어요. ㅋ 아,초한지여파로 풍소봉 한국방문좀ㅋ</td>
      <td>1</td>
      <td>[-0.025938444, -0.055467058, -0.028613266, 0.0...</td>
    </tr>
  </tbody>
</table>
<p>2000 rows × 4 columns</p>
</div>



<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>CONVERT USING</strong>" 쿼리 구문은 <code>nsmc_text_search_model</code>을 텍스트 수치화를 위한 알고리즘으로 사용합니다.</li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문을 통해 텍스트 수치화 시 필요한 변수들을 정의합니다.
        <ul>
            <li>"text_col": 데이터 테이블에서 영화 리뷰 데이터를 담은 컬럼 (default: "text")</li>
            <li>"table_name": ThanoSQL 워크스페이스 데이터베이스 내에 저장될 테이블 이름을 정의합니다.</li>
            <li>"batch_size": 한 번의 예측에서 읽는 데이터 세트 묶음의 크기 (int, default: 16)</li>
            <li>"column_name": 데이터 테이블에서 수치화된 결과를 담을 컬럼 이름을 정의합니다. (default: "convert_result")</li>
        </ul>
        </li>
    </ul>
</div>

## __3. 텍스트 수치화 모델을 사용해서 유사한 문서 검색하기__

이번 단계에서는 <mark style="background-color:#E9D7FD">nsmc_text_search_model</mark> 텍스트 수치화 모델과 테스트 테이블을 사용하여 유사한 문서를 검색합니다.


```python
%%thanosql
SELECT document, label, score 
FROM (
    SEARCH TEXT text="영화 내용이 불만족스러웠다"
    USING nsmc_text_search_model
    OPTIONS (
        emb_col="convert_result",
        column_name="score"
        )
    AS 
    SELECT * 
    FROM nsmc_test
    )
ORDER BY score DESC 
LIMIT 10
```




<div class="df_size">
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>document</th>
      <th>label</th>
      <th>score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>잔잔한 영화 보기 불편할수도 있음</td>
      <td>1</td>
      <td>0.659124</td>
    </tr>
    <tr>
      <th>1</th>
      <td>영화로서는 많이 아쉬운 듯..</td>
      <td>0</td>
      <td>0.642425</td>
    </tr>
    <tr>
      <th>2</th>
      <td>너무 작위적인 스토리 전개..실망스러운 영화ㅠ</td>
      <td>0</td>
      <td>0.642100</td>
    </tr>
    <tr>
      <th>3</th>
      <td>시나리오는 한없이 후진데다가 썩은 동선이 가득한 영화</td>
      <td>0</td>
      <td>0.633289</td>
    </tr>
    <tr>
      <th>4</th>
      <td>가벼운 마음으로 웃으면서 영화를 따라가다보니 어느새 끝나있는 영화</td>
      <td>1</td>
      <td>0.625073</td>
    </tr>
    <tr>
      <th>5</th>
      <td>정말 싱겁다 영화를 다봣어도 보다만 느낌이다</td>
      <td>0</td>
      <td>0.623635</td>
    </tr>
    <tr>
      <th>6</th>
      <td>부족한 감이 있었고, 암울했다.</td>
      <td>0</td>
      <td>0.618519</td>
    </tr>
    <tr>
      <th>7</th>
      <td>내가 생각했던 스토리와 조금 달랐지만 신선하고 독특했던 영화였다.</td>
      <td>1</td>
      <td>0.616638</td>
    </tr>
    <tr>
      <th>8</th>
      <td>평점보고 기대해서 그런지 실망스런 영화 ,쏘쏘한 영화</td>
      <td>0</td>
      <td>0.610020</td>
    </tr>
    <tr>
      <th>9</th>
      <td>무관심했던 나를 반성케 한 영화</td>
      <td>1</td>
      <td>0.603797</td>
    </tr>
  </tbody>
</table>
</div>




```python
%%thanosql
SELECT document, label, score
FROM (
    SEARCH TEXT text="기분이 좋아지는 작품"
    USING nsmc_text_search_model
    OPTIONS (
        emb_col="convert_result",
        column_name="score"
        )
    AS
    SELECT *
    FROM nsmc_test
    )
ORDER BY score DESC
LIMIT 10
```




<div class="df_size">
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>document</th>
      <th>label</th>
      <th>score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>보고나면 기분 좋은 영화</td>
      <td>1</td>
      <td>0.773304</td>
    </tr>
    <tr>
      <th>1</th>
      <td>감동적인 영화</td>
      <td>1</td>
      <td>0.719860</td>
    </tr>
    <tr>
      <th>2</th>
      <td>너무 멋있는 영화. 환한 느낌을 준다</td>
      <td>1</td>
      <td>0.708152</td>
    </tr>
    <tr>
      <th>3</th>
      <td>몰입도 잘되고 좋은 영화</td>
      <td>1</td>
      <td>0.693896</td>
    </tr>
    <tr>
      <th>4</th>
      <td>오래됐지만 재미있는 영화</td>
      <td>1</td>
      <td>0.688140</td>
    </tr>
    <tr>
      <th>5</th>
      <td>뻔한 스토리지만 과거 추억을 생각나게 해주는 영화 재미있었어요</td>
      <td>1</td>
      <td>0.678594</td>
    </tr>
    <tr>
      <th>6</th>
      <td>멋진영화</td>
      <td>1</td>
      <td>0.678019</td>
    </tr>
    <tr>
      <th>7</th>
      <td>인상 깊은 영화다~</td>
      <td>1</td>
      <td>0.671168</td>
    </tr>
    <tr>
      <th>8</th>
      <td>오랜만에 좋은 영화를 보았다.</td>
      <td>1</td>
      <td>0.667201</td>
    </tr>
    <tr>
      <th>9</th>
      <td>추억속의 영화 ㅎ 정말 재밌게 봤습니다!</td>
      <td>1</td>
      <td>0.663330</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>SEARCH TEXT [image|audio|video|text|keyword]</strong>" 쿼리 구문은 검색하고자 하는 이미지|오디오|비디오|텍스트|키워드 데이터를 정의합니다.</li>
        <li>"<strong>USING</strong>"은 텍스트 수치화에 사용할 모델을 정의합니다.</li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문을 통해 텍스트 검색 시 필요한 변수들을 정의합니다.
        <ul>
                <li>"emb_col": 데이터 테이블에서 수치화된 결과를 담은 컬럼</li>
                <li>"column_name": 데이터 테이블에서 검색 결과를 담을 컬럼 이름을 정의합니다. (default: "search_result")</li>
        </ul>
        </li>
        <li>"<strong>AS</strong>" 쿼리 구문은 검색에 사용할 임베딩 테이블을 정의합니다. <code>nsmc_test</code> 테이블을 사용합니다.</li>
    </ul>
</div>

## __4. 텍스트 수치화 모델을 사용해서 문서에서 키워드 추출하기__

이번 단계에서는 <mark style="background-color:#E9D7FD">nsmc_text_search_model</mark> 텍스트 수치화 모델과 테스트 테이블을 사용하여 유사한 문서에서 키워드 추출합니다. 


```python
%%thanosql
SEARCH KEYWORD
USING nsmc_text_search_model
OPTIONS (
    text_col="document",
    ngram_range=[1, 3],
    use_stopwords=True
    )
AS 
SELECT * 
FROM nsmc_test
LIMIT 10 OFFSET 40
```




<div class="df_size">
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>document</th>
      <th>label</th>
      <th>convert_result</th>
      <th>keyword</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>7928591</td>
      <td>일단 엠씨 역량 부족이 가장 큰 문제인듯. 시간 없다고, 재미없다고 게스트 말 끊는...</td>
      <td>0</td>
      <td>[0.012267594, -0.01771399, -0.010869383, -0.00...</td>
      <td>{'keyword': ['라고 끝 엠씨가노답임', '방 왜 ᆷ', '가장 크 문제'...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>8380896</td>
      <td>방금 슬쩍 봤는데 '인삼' 처럼 생긴 '산삼' 암수가 서로 토닥이다가 주인공에게 잡...</td>
      <td>0</td>
      <td>[-0.042678047, -0.02154563, -0.0577272, -0.022...</td>
      <td>{'keyword': ['산삼 암수 서로', '주인공 잡히 ᆷ', '는데 인삼 처럼...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>8914833</td>
      <td>신세계에 비교하는 거 진심 빡친다. 신성모독</td>
      <td>1</td>
      <td>[0.026429567, -0.014425963, -0.0066649574, 0.0...</td>
      <td>{'keyword': ['빡 치 신성모독', '거 진심', '진심 빡 치', '신세...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>9702804</td>
      <td>킬링타임 최고다 열자채워</td>
      <td>1</td>
      <td>[0.019156558, -0.025161155, -0.042373788, 0.02...</td>
      <td>{'keyword': ['킬링타임 최고 다', '킬링타임', '최고 다 열', '열...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>10097178</td>
      <td>명불허전 선동영화 10자</td>
      <td>0</td>
      <td>[-0.010032367, -0.020877095, 0.02225531, -0.04...</td>
      <td>{'keyword': ['명불허전 선동 영화', '명불허전 선동', '명불허전', ...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>9245590</td>
      <td>결말이 마음에 든다. 소설을 먼저 읽었던 터라 결말이 마음에 든다. 세상의 모든 정...</td>
      <td>1</td>
      <td>[-0.009547141, -0.03575876, 0.022106346, -0.00...</td>
      <td>{'keyword': ['결말 마음 들', '모든 정혜 힘내', '들 세상 모든',...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>4227156</td>
      <td>기대했는데.. 시나리오는 ...어떻게 결말이 그렇지?..요즘 막장 드라마 영향인가..</td>
      <td>0</td>
      <td>[0.03098442, -0.05774053, 0.033200644, 0.01355...</td>
      <td>{'keyword': ['시나리오 어떻 결말', '드라마 영향 ᆫ가', '요즘 막장...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>7313784</td>
      <td>인기연예인 몇명 출현한다고 재미있는 영화냐 잡것들아긴급조치19호가 그렇게 만들고 망...</td>
      <td>0</td>
      <td>[0.025257412, -0.030326752, 0.02663638, 0.0056...</td>
      <td>{'keyword': ['영화 냐 잡것', '인기 연예인 몇', '출현 ᆫ다고', ...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>3582981</td>
      <td>좋았음!!</td>
      <td>1</td>
      <td>[0.0419066, -0.00032061862, -0.037271593, 0.03...</td>
      <td>{'keyword': ['좋', '좋 음', '음'], 'score': [0.547...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>9906269</td>
      <td>멜로와 스릴러를 이렇게 조합할수 있다니 ...</td>
      <td>1</td>
      <td>[-0.022876328, -0.071984485, 0.009473692, 0.00...</td>
      <td>{'keyword': ['멜로 스릴러', '스릴러 이렇 조합', '조합', '이렇 ...</td>
    </tr>
  </tbody>
</table>
</div>




```python
%%thanosql
SELECT document, label, keyword -> 'keyword' AS keywords, keyword -> 'score' AS score
FROM (
    SEARCH KEYWORD 
    USING nsmc_text_search_model
    OPTIONS (
        text_col="document",
        use_stopwords=True
        )
    AS 
    SELECT * 
    FROM nsmc_test
    LIMIT 10
    )
```




<div class="df_size">
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>document</th>
      <th>label</th>
      <th>keywords</th>
      <th>score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>꼭 한번 봐야하는 영화인 것 같네요 ㅎㅎ</td>
      <td>1</td>
      <td>[영화 네요, 꼭 번, 네요, 번 영화, 꼭]</td>
      <td>[0.6508, 0.5355, 0.5246, 0.4709, 0.4693]</td>
    </tr>
    <tr>
      <th>1</th>
      <td>권선징악은안드로메다로~럽라인은작가빙의된니끼한조연과~여주는남자갖고논질나쁜여자</td>
      <td>0</td>
      <td>[권선징악 안드로메다, 빙의 니, ~여주 남자, 질 나쁘, 라인 작가]</td>
      <td>[0.4953, 0.3959, 0.3657, 0.2388, 0.2272]</td>
    </tr>
    <tr>
      <th>2</th>
      <td>유쾌하고 신나는 스피드 코미디.</td>
      <td>1</td>
      <td>[스피드 코미디, 유쾌, 코미디, 스피드, 신 나]</td>
      <td>[0.7569, 0.6539, 0.5764, 0.574, 0.4409]</td>
    </tr>
    <tr>
      <th>3</th>
      <td>정말 따뜻했던 드라마ㅠㅠ</td>
      <td>1</td>
      <td>[정말 따뜻하, 따뜻하 드라마, 정말, 따뜻하, 드라마]</td>
      <td>[0.8149, 0.804, 0.7173, 0.7024, 0.5407]</td>
    </tr>
    <tr>
      <th>4</th>
      <td>솔직히 배우들의 연기는 나름 괜찮았다 하지만 냉정하게 영화자체만을 놓고보면 별다른 ...</td>
      <td>0</td>
      <td>[연출력 그냥, 전개, 별다르 특색, 살인마 주인공, 하지만 냉정]</td>
      <td>[0.4769, 0.3703, 0.3028, 0.3021, 0.2695]</td>
    </tr>
    <tr>
      <th>5</th>
      <td>느와르에 멜로에 신파극까지······. 짬뽕이 아니라 꿀꿀이죽</td>
      <td>0</td>
      <td>[신파극 짬뽕, 짬뽕 꿀꿀이죽, 느와르, 짬뽕, 멜로]</td>
      <td>[0.5395, 0.4998, 0.4831, 0.3861, 0.3857]</td>
    </tr>
    <tr>
      <th>6</th>
      <td>다시 보고 싶네요~</td>
      <td>1</td>
      <td>[다시 싶, 싶 네요, 다시, 네요, 싶]</td>
      <td>[0.6543, 0.6535, 0.6249, 0.5935, 0.3829]</td>
    </tr>
    <tr>
      <th>7</th>
      <td>짱이야... 진짜 극장을 나오는데 멋진 소설을 한권 읽은 듯한 기분을 느낌. 재미는...</td>
      <td>1</td>
      <td>[감동 철철, 짱 야, 영화 꼭, 재미 물론, 권 읽]</td>
      <td>[0.5069, 0.4895, 0.4673, 0.3759, 0.3449]</td>
    </tr>
    <tr>
      <th>8</th>
      <td>유일하게 아들과 같이볼수있는 만화영화</td>
      <td>1</td>
      <td>[만화 영화, 아들 같이, 유일 아들, 유일, 같이 있]</td>
      <td>[0.534, 0.4989, 0.4354, 0.3868, 0.3645]</td>
    </tr>
    <tr>
      <th>9</th>
      <td>남자라면 봐라 꼭봐라 두번봐라 세번봐라 계속봐라</td>
      <td>1</td>
      <td>[계속 어라, 세 번, 꼭, 남자 라면, 보]</td>
      <td>[0.4975, 0.3957, 0.368, 0.2101, 0.1979]</td>
    </tr>
  </tbody>
</table>
</div>




```python
%%thanosql 
SELECT * FROM (
    SELECT document, label, json_array_elements(keyword -> 'keyword') AS keywords, (json_array_elements(keyword -> 'score'))::text::float AS score
        FROM (
            SEARCH KEYWORD 
            USING nsmc_text_search_model
            OPTIONS (
                text_col="document",
                use_stopwords=True
                )
            AS 
            SELECT * 
            FROM nsmc_test
            LIMIT 10
        )
    ) 
WHERE score > 0.5
```




<div class="df_size">
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>document</th>
      <th>label</th>
      <th>keywords</th>
      <th>score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>꼭 한번 봐야하는 영화인 것 같네요 ㅎㅎ</td>
      <td>1</td>
      <td>영화 네요</td>
      <td>0.6508</td>
    </tr>
    <tr>
      <th>1</th>
      <td>꼭 한번 봐야하는 영화인 것 같네요 ㅎㅎ</td>
      <td>1</td>
      <td>꼭 번</td>
      <td>0.5355</td>
    </tr>
    <tr>
      <th>2</th>
      <td>꼭 한번 봐야하는 영화인 것 같네요 ㅎㅎ</td>
      <td>1</td>
      <td>네요</td>
      <td>0.5246</td>
    </tr>
    <tr>
      <th>3</th>
      <td>유쾌하고 신나는 스피드 코미디.</td>
      <td>1</td>
      <td>스피드 코미디</td>
      <td>0.7569</td>
    </tr>
    <tr>
      <th>4</th>
      <td>유쾌하고 신나는 스피드 코미디.</td>
      <td>1</td>
      <td>유쾌</td>
      <td>0.6539</td>
    </tr>
    <tr>
      <th>5</th>
      <td>유쾌하고 신나는 스피드 코미디.</td>
      <td>1</td>
      <td>코미디</td>
      <td>0.5764</td>
    </tr>
    <tr>
      <th>6</th>
      <td>유쾌하고 신나는 스피드 코미디.</td>
      <td>1</td>
      <td>스피드</td>
      <td>0.5740</td>
    </tr>
    <tr>
      <th>7</th>
      <td>정말 따뜻했던 드라마ㅠㅠ</td>
      <td>1</td>
      <td>정말 따뜻하</td>
      <td>0.8149</td>
    </tr>
    <tr>
      <th>8</th>
      <td>정말 따뜻했던 드라마ㅠㅠ</td>
      <td>1</td>
      <td>따뜻하 드라마</td>
      <td>0.8040</td>
    </tr>
    <tr>
      <th>9</th>
      <td>정말 따뜻했던 드라마ㅠㅠ</td>
      <td>1</td>
      <td>정말</td>
      <td>0.7173</td>
    </tr>
    <tr>
      <th>10</th>
      <td>정말 따뜻했던 드라마ㅠㅠ</td>
      <td>1</td>
      <td>따뜻하</td>
      <td>0.7024</td>
    </tr>
    <tr>
      <th>11</th>
      <td>정말 따뜻했던 드라마ㅠㅠ</td>
      <td>1</td>
      <td>드라마</td>
      <td>0.5407</td>
    </tr>
    <tr>
      <th>12</th>
      <td>느와르에 멜로에 신파극까지······. 짬뽕이 아니라 꿀꿀이죽</td>
      <td>0</td>
      <td>신파극 짬뽕</td>
      <td>0.5395</td>
    </tr>
    <tr>
      <th>13</th>
      <td>다시 보고 싶네요~</td>
      <td>1</td>
      <td>다시 싶</td>
      <td>0.6543</td>
    </tr>
    <tr>
      <th>14</th>
      <td>다시 보고 싶네요~</td>
      <td>1</td>
      <td>싶 네요</td>
      <td>0.6535</td>
    </tr>
    <tr>
      <th>15</th>
      <td>다시 보고 싶네요~</td>
      <td>1</td>
      <td>다시</td>
      <td>0.6249</td>
    </tr>
    <tr>
      <th>16</th>
      <td>다시 보고 싶네요~</td>
      <td>1</td>
      <td>네요</td>
      <td>0.5935</td>
    </tr>
    <tr>
      <th>17</th>
      <td>짱이야... 진짜 극장을 나오는데 멋진 소설을 한권 읽은 듯한 기분을 느낌. 재미는...</td>
      <td>1</td>
      <td>감동 철철</td>
      <td>0.5069</td>
    </tr>
    <tr>
      <th>18</th>
      <td>유일하게 아들과 같이볼수있는 만화영화</td>
      <td>1</td>
      <td>만화 영화</td>
      <td>0.5340</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>SEARCH KEYWORD</strong>" 쿼리 구문은 키워드를 검색하기 위한 알고리즘으로 사용합니다.</li>
        <li>"<strong>USING</strong>"은 텍스트 수치화에 사용할 모델을 정의합니다.</li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문을 통해 텍스트 수치화 시 필요한 변수들을 정의합니다.
            <ul>
                <li>"lang": en, ko</li>
                <li>"text_col": 텍스트가 있는 열 이름</li>
                <li>"ngram_range": 키워드의 최소 단어 수와 최대 단어수. 예) [1, 3]. 대부분의 상황에서 키워드는 최대 단어 수에 맞춰 추출됩니다 (list[int, int], default: [1, 2])</li>
                <li>"top_n": 추출할 키워드의 수, 유사도가 높은 순서대로 (int, default: 5)</li>
                <li>"diversity": 추출될 키워드의 다양성. 높을 수록 기존에 추출된 키워드와 유사한 키워드는 다시 추출되지 않습니다. 0 <= diversity <= 1 (float, default: 0.5)</li>
                <li>"use_stopwords": 큰 의미가 없는 단어(불용어)를 제외할 지 여부 (True|False, default: True)</li>
                <li>"threshold": 추출할 키워드의 유사도 수치의 최소값 (float, default: 0.0)</li>
            </ul>
            </li>
        <li>"<strong>AS</strong>" 쿼리 구문은 검색에 사용할 임베딩 테이블을 정의합니다. <code>nsmc_test</code> 테이블을 사용합니다.</li>
    </ul>
</div>

## __5. 두 방법 결합하여 사용하기__


```python
%%thanosql
SEARCH KEYWORD
USING nsmc_text_search_model
OPTIONS (
    text_col="document",
    ngram_range=[1, 3],
    use_stopwords=True
    )
AS (
    SELECT document, label, score
    FROM (
        SEARCH TEXT text="가볍게 볼 수 있는 코미디 영화"
        USING nsmc_text_search_model
        OPTIONS (
            emb_col = "convert_result",
            column_name="score"
            )
        AS 
        SELECT * 
        FROM nsmc_test
        )
    ORDER BY score DESC 
    LIMIT 10
    )
```




<div class="df_size">
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>document</th>
      <th>label</th>
      <th>score</th>
      <th>keyword</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>순수하게 보면 참으로 재밌는 영화</td>
      <td>1</td>
      <td>0.745830</td>
      <td>{'keyword': ['참으로 재밌 영화', '순수 보 참으로', '보 참으로',...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>오래됐지만 재미있는 영화</td>
      <td>1</td>
      <td>0.692706</td>
      <td>{'keyword': ['오래되 재미있 영화', '재미있 영화', '오래되 재미있'...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>보고나면 기분 좋은 영화</td>
      <td>1</td>
      <td>0.692283</td>
      <td>{'keyword': ['기분 좋 영화', '좋 영화', '기분 좋', '영화', ...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>감동적인 영화</td>
      <td>1</td>
      <td>0.684076</td>
      <td>{'keyword': ['감동 영화', '감동', '영화'], 'score': [0...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>코미디로도 사람의 마음을 울릴수 있느 영화</td>
      <td>1</td>
      <td>0.678831</td>
      <td>{'keyword': ['울리 있느 영화', '코미디 마음 울리', '코미디 마음'...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>시간가는줄 모르고 잼있게봤다 볼만한 영화</td>
      <td>1</td>
      <td>0.670244</td>
      <td>{'keyword': ['보 만 영화', '모르 잼 보', '시간 가', '줄', ...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>레전드란 말이 어울리는 몇 안되는 영화중 하나</td>
      <td>1</td>
      <td>0.661960</td>
      <td>{'keyword': ['레전드 란 어울리', '몇 안 영화', '어울리 몇 안',...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>가벼운 마음으로 웃으면서 영화를 따라가다보니 어느새 끝나있는 영화</td>
      <td>1</td>
      <td>0.648359</td>
      <td>{'keyword': ['어느새 끝나 영화', '마음 웃 으면서', '영화 따라가 ...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>간만에 본 한국영화 중 수작중의 수작</td>
      <td>1</td>
      <td>0.647729</td>
      <td>{'keyword': ['간만에 한국 영화', '한국 영화 수작', '영화 수작 수...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>모든것이 흥미로운 영화였다.</td>
      <td>1</td>
      <td>0.640093</td>
      <td>{'keyword': ['모든 흥미', '모든 흥미 롭', '흥미 롭 영화', '모...</td>
    </tr>
  </tbody>
</table>
</div>




```python
%%thanosql
SELECT document, label, keyword -> 'keyword' AS keywords, keyword -> 'score' AS score
FROM ( 
    SEARCH KEYWORD
    USING nsmc_text_search_model
    OPTIONS (
        text_col="document",
        ngram_range=[1, 3],
        use_stopwords=True
        )
    AS (
        SELECT document, label, score
        FROM (
            SEARCH TEXT text="가볍게 볼 수 있는 코미디 영화"
            USING nsmc_text_search_model
            OPTIONS (
                emb_col = "convert_result",
                column_name="score"
                )
            AS 
            SELECT * 
            FROM nsmc_test
            )
        ORDER BY score DESC 
        LIMIT 10
        )
    )
```




<div class="df_size">
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>document</th>
      <th>label</th>
      <th>keywords</th>
      <th>score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>순수하게 보면 참으로 재밌는 영화</td>
      <td>1</td>
      <td>[참으로 재밌 영화, 순수 보 참으로, 보 참으로, 영화, 순수]</td>
      <td>[0.7928, 0.6529, 0.5572, 0.5498, 0.5255]</td>
    </tr>
    <tr>
      <th>1</th>
      <td>오래됐지만 재미있는 영화</td>
      <td>1</td>
      <td>[오래되 재미있 영화, 재미있 영화, 오래되 재미있, 영화, 오래되]</td>
      <td>[0.7788, 0.7097, 0.6668, 0.6538, 0.653]</td>
    </tr>
    <tr>
      <th>2</th>
      <td>보고나면 기분 좋은 영화</td>
      <td>1</td>
      <td>[기분 좋 영화, 좋 영화, 기분 좋, 영화, 나 기분]</td>
      <td>[0.8424, 0.7348, 0.715, 0.6244, 0.579]</td>
    </tr>
    <tr>
      <th>3</th>
      <td>감동적인 영화</td>
      <td>1</td>
      <td>[감동 영화, 감동, 영화]</td>
      <td>[0.885, 0.7347, 0.6884]</td>
    </tr>
    <tr>
      <th>4</th>
      <td>코미디로도 사람의 마음을 울릴수 있느 영화</td>
      <td>1</td>
      <td>[울리 있느 영화, 코미디 마음 울리, 코미디 마음, 마음, 영화]</td>
      <td>[0.662, 0.6117, 0.4975, 0.4285, 0.4237]</td>
    </tr>
    <tr>
      <th>5</th>
      <td>시간가는줄 모르고 잼있게봤다 볼만한 영화</td>
      <td>1</td>
      <td>[보 만 영화, 모르 잼 보, 시간 가, 줄, 가 줄 모르]</td>
      <td>[0.5494, 0.476, 0.4309, 0.3598, 0.3372]</td>
    </tr>
    <tr>
      <th>6</th>
      <td>레전드란 말이 어울리는 몇 안되는 영화중 하나</td>
      <td>1</td>
      <td>[레전드 란 어울리, 몇 안 영화, 어울리 몇 안, 영화 하나, 하나]</td>
      <td>[0.5986, 0.5929, 0.4756, 0.4671, 0.3399]</td>
    </tr>
    <tr>
      <th>7</th>
      <td>가벼운 마음으로 웃으면서 영화를 따라가다보니 어느새 끝나있는 영화</td>
      <td>1</td>
      <td>[어느새 끝나 영화, 마음 웃 으면서, 영화 따라가 보, 보 니 어느새, 가볍]</td>
      <td>[0.71, 0.4958, 0.4768, 0.424, 0.3438]</td>
    </tr>
    <tr>
      <th>8</th>
      <td>간만에 본 한국영화 중 수작중의 수작</td>
      <td>1</td>
      <td>[간만에 한국 영화, 한국 영화 수작, 영화 수작 수작, 수작 수작, 영화]</td>
      <td>[0.718, 0.6774, 0.6634, 0.5333, 0.494]</td>
    </tr>
    <tr>
      <th>9</th>
      <td>모든것이 흥미로운 영화였다.</td>
      <td>1</td>
      <td>[모든 흥미, 모든 흥미 롭, 흥미 롭 영화, 모든, 롭 영화]</td>
      <td>[0.6237, 0.6141, 0.5517, 0.5436, 0.5123]</td>
    </tr>
  </tbody>
</table>
</div>




```python
%%thanosql
SELECT * 
FROM 
    (SELECT document, label, json_array_elements(keyword -> 'keyword') AS keywords, (json_array_elements(keyword -> 'score'))::text::float AS score
    FROM (
        SEARCH KEYWORD
        USING nsmc_text_search_model
        OPTIONS (
            text_col="document",
            ngram_range=[1, 3],
            use_stopwords=True
            )
        AS (
            SELECT document, label, score
            FROM (
                SEARCH TEXT text="최고의 액션 영화"
                USING nsmc_text_search_model
                OPTIONS (
                    emb_col = "convert_result",
                    column_name="score"
                    )
                AS 
                SELECT * 
                FROM nsmc_test
                WHERE document LIKE '%%판타지%%'
                )
            ORDER BY score DESC
            LIMIT 10
            )
        )
    )
WHERE score > 0.3
```




<div class="df_size">
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>document</th>
      <th>label</th>
      <th>keywords</th>
      <th>score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>정말 재밌다.이런게 바로 판타지모험영화의 현실이다.</td>
      <td>1</td>
      <td>바로 판타지 모험</td>
      <td>0.6523</td>
    </tr>
    <tr>
      <th>1</th>
      <td>정말 재밌다.이런게 바로 판타지모험영화의 현실이다.</td>
      <td>1</td>
      <td>이런 바로 판타지</td>
      <td>0.6060</td>
    </tr>
    <tr>
      <th>2</th>
      <td>정말 재밌다.이런게 바로 판타지모험영화의 현실이다.</td>
      <td>1</td>
      <td>정말 재밌 이런</td>
      <td>0.5733</td>
    </tr>
    <tr>
      <th>3</th>
      <td>정말 재밌다.이런게 바로 판타지모험영화의 현실이다.</td>
      <td>1</td>
      <td>영화</td>
      <td>0.4376</td>
    </tr>
    <tr>
      <th>4</th>
      <td>정말 재밌다.이런게 바로 판타지모험영화의 현실이다.</td>
      <td>1</td>
      <td>현실</td>
      <td>0.4012</td>
    </tr>
    <tr>
      <th>5</th>
      <td>30년 전의 퓨전 판타지라니..그냥 찬양해야됨</td>
      <td>1</td>
      <td>그냥 찬양 ᆷ</td>
      <td>0.6318</td>
    </tr>
    <tr>
      <th>6</th>
      <td>30년 전의 퓨전 판타지라니..그냥 찬양해야됨</td>
      <td>1</td>
      <td>퓨전 판타지 라니</td>
      <td>0.5863</td>
    </tr>
    <tr>
      <th>7</th>
      <td>30년 전의 퓨전 판타지라니..그냥 찬양해야됨</td>
      <td>1</td>
      <td>라니 그냥</td>
      <td>0.4737</td>
    </tr>
    <tr>
      <th>8</th>
      <td>30년 전의 퓨전 판타지라니..그냥 찬양해야됨</td>
      <td>1</td>
      <td>찬양</td>
      <td>0.4026</td>
    </tr>
    <tr>
      <th>9</th>
      <td>1700만 1700만 해되서 봤다가 욕만하며 참고 참으며 보다 결국 사분의 일은 스...</td>
      <td>0</td>
      <td>참 으며 보다</td>
      <td>0.3967</td>
    </tr>
    <tr>
      <th>10</th>
      <td>1700만 1700만 해되서 봤다가 욕만하며 참고 참으며 보다 결국 사분의 일은 스...</td>
      <td>0</td>
      <td>일 스킵 영화</td>
      <td>0.3763</td>
    </tr>
    <tr>
      <th>11</th>
      <td>1700만 1700만 해되서 봤다가 욕만하며 참고 참으며 보다 결국 사분의 일은 스...</td>
      <td>0</td>
      <td>충무공 부끄럽 지경</td>
      <td>0.3427</td>
    </tr>
    <tr>
      <th>12</th>
      <td>1700만 1700만 해되서 봤다가 욕만하며 참고 참으며 보다 결국 사분의 일은 스...</td>
      <td>0</td>
      <td>결국 사 분</td>
      <td>0.3142</td>
    </tr>
    <tr>
      <th>13</th>
      <td>이거 판타지영화냐? 맙소사 ...뭘말하고싶은거냐 이건 여자도 공감이안가는 영화야...</td>
      <td>0</td>
      <td>영화 냐 맙소사</td>
      <td>0.5521</td>
    </tr>
    <tr>
      <th>14</th>
      <td>이거 판타지영화냐? 맙소사 ...뭘말하고싶은거냐 이건 여자도 공감이안가는 영화야...</td>
      <td>0</td>
      <td>이거 판타지</td>
      <td>0.5067</td>
    </tr>
    <tr>
      <th>15</th>
      <td>이거 판타지영화냐? 맙소사 ...뭘말하고싶은거냐 이건 여자도 공감이안가는 영화야...</td>
      <td>0</td>
      <td>냐 이거 여자</td>
      <td>0.5003</td>
    </tr>
    <tr>
      <th>16</th>
      <td>이거 판타지영화냐? 맙소사 ...뭘말하고싶은거냐 이건 여자도 공감이안가는 영화야...</td>
      <td>0</td>
      <td>공감 안 가</td>
      <td>0.4512</td>
    </tr>
    <tr>
      <th>17</th>
      <td>이거 판타지영화냐? 맙소사 ...뭘말하고싶은거냐 이건 여자도 공감이안가는 영화야...</td>
      <td>0</td>
      <td>뭐 ᆯ 싶</td>
      <td>0.4128</td>
    </tr>
    <tr>
      <th>18</th>
      <td>의도가뭐여 그냥즐기라는건지 아무리판타지라지만도통모르것네</td>
      <td>0</td>
      <td>의도가뭐여 그냥 즐기</td>
      <td>0.7562</td>
    </tr>
    <tr>
      <th>19</th>
      <td>의도가뭐여 그냥즐기라는건지 아무리판타지라지만도통모르것네</td>
      <td>0</td>
      <td>의도가뭐여</td>
      <td>0.6973</td>
    </tr>
    <tr>
      <th>20</th>
      <td>의도가뭐여 그냥즐기라는건지 아무리판타지라지만도통모르것네</td>
      <td>0</td>
      <td>라지만 도통 모르</td>
      <td>0.5431</td>
    </tr>
    <tr>
      <th>21</th>
      <td>의도가뭐여 그냥즐기라는건지 아무리판타지라지만도통모르것네</td>
      <td>0</td>
      <td>즐기 거 ᆫ지</td>
      <td>0.3882</td>
    </tr>
    <tr>
      <th>22</th>
      <td>의도가뭐여 그냥즐기라는건지 아무리판타지라지만도통모르것네</td>
      <td>0</td>
      <td>ᆫ지 아무리 판타지</td>
      <td>0.3698</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>SEARCH TEXT [image|audio|video|text|keyword]</strong>" 쿼리 구문은 검색하고자 하는 이미지|오디오|비디오|텍스트|키워드 데이터를 정의합니다.</li>
        <li>"<strong>SEARCH KEYWORD</strong>" 쿼리 구문은 키워드를 검색하기 위한 알고리즘으로 사용합니다.</li>
        <li>"<strong>USING</strong>"은 텍스트 수치화에 사용할 모델을 정의합니다.</li>
        <li>"<strong>AS</strong>" 쿼리 구문은 검색에 사용할 임베딩 테이블을 정의합니다. <code>nsmc_test</code> 테이블을 사용합니다.</li>
    </ul>
</div>

## __6. 튜토리얼을 마치며__

이번 튜토리얼에서는 `nsmc` 영화 리뷰 데이터를 사용하여 텍스트 수치화와 수치화 결과를 바탕으로 한 유사 텍스트 검색, 키워드 추출을 진행해 보았습니다. 이번 튜토리얼에서는 모델의 수치화 성능보다는 작동 위주의 설명으로 진행하였습니다. 나만의 텍스트 수치화모델을 만들어 다양한 형태의 비정형 데이터 세트에 검색 기능을 추가하고 Auto-ML 기법을 이용한 나만의 모델을 배포할 수 있습니다.

* [나만의 데이터 업로드하기](https://docs.thanosql.ai/ko/getting_started/data_upload/)
* [나만의 데이터 테이블 생성하기](https://docs.thanosql.ai/ko/how-to_guides/ThanoSQL_query/COPY_SYNTAX/)
* [나만의 모델 업로드하기](https://docs.thanosql.ai/ko/how-to_guides/ThanoSQL_query/UPLOAD_SYNTAX/)

<div class="admonition tip">
    <h4 class="admonition-title">나만의 서비스를 위한 모델 배포 관련 문의</h4>
    <p>ThanoSQL을 활용해 나만의 모델을 만들거나, 나의 서비스에 적용하는데 어려움이 있다면 언제든 아래로 문의주세요😊</p>
    <p>유사 텍스트 검색 모델 구축 관련 문의: contact@smartmind.team</p>
</div>
