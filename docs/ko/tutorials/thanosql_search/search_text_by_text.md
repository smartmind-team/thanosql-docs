---
title: 텍스트로 텍스트 검색하기
---

# __텍스트로 텍스트 검색하기__

- 튜토리얼 난이도 : ★☆☆☆☆
- 읽는데 걸리는 시간 : 7분
- 사용 언어 : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 실행 파일 위치 : tutorial/thanosql_search/search_text_by_text.ipynb   
- 참고 문서 : [단어 임베딩: 어휘의 의미(LEXICAL SEMANTICS)를 인코딩하기](https://tutorials.pytorch.kr/beginner/nlp/word_embeddings_tutorial.html), [컴퓨터가 바라보는 문자](https://heung-bae-lee.github.io/2020/01/16/NLP_01/)

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

ThanoSQL의 쿼리 구문을 사용하기 위해서는 [ThanoSQL 워크스페이스](https://docs.thanosql.ai/getting_started/how_to_use_ThanoSQL/#5-thanosql)
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
            <li>"overwrite" : 동일 이름의 데이터 세트가 존재하는 경우 덮어쓰기 가능 유무 설정. True일 경우 기존 데이터 세트는 새로운 데이터 세트로 변경됨 (True|False, DEFAULT : False) </li>
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
        <li>"<strong>COPY</strong>" 쿼리 구문을 사용하여 DB에 저장 할 데이터 세트명을 지정합니다. </li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문을 통해 <strong>COPY</strong> 에 사용할 옵션을 지정합니다.
        <ul>
            <li>"overwrite" : 동일 이름의 데이터 세트가 DB상에 존재하는 경우 덮어쓰기 가능 유무 설정. True일 경우 기존 데이터 세트는 새로운 데이터 세트로 변경됨 (True|False, DEFAULT : False) </li>
        </ul>
        </li>
    </ul>
</div>

## __1. 데이터 세트 확인__

텍스트 수치화 모델을 만들기 위해 ThanoSQL DB에 저장되어 있는 <mark style="background-color:#FFEC92">nsmc_train</mark> 테이블을 사용합니다. <mark style="background-color:#FFEC92">nsmc_train</mark> 테이블은 <mark style="background-color:#FFD79C">NAVER Sentiment Movie Corpus</mark> 영화 리뷰 데이터 및 라벨 정보의 일부가 담겨 있는 테이블입니다. 아래의 쿼리문을 실행하고 테이블의 내용을 확인합니다.


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
        <li><mark style="background-color:#D7D0FF">label</mark> : 라벨값</li>
    </ul>
</div>

## __2. 텍스트 수치화 모델 생성__

이전 단계에서 확인한 <mark style="background-color:#FFEC92">nsmc_train</mark> 테이블을 사용하여 텍스트 수치화 모델을 만듭니다. 아래의 쿼리 구문을 실행하여 <mark style="background-color:#E9D7FD">nsmc_text_search_model</mark>이라는 이름의 모델을 만듭니다.  
(쿼리 실행 시 예상 소요 시간 : 2 min)


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

    Building model...
    Success


<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>BUILD MODEL</strong>" 쿼리 구문을 사용하여 <mark style="background-color:#E9D7FD">nsmc_text_search_model</mark> 이라는 모델을 만들고 학습시킵니다.</li>
        <li>"<strong>USING</strong>" 쿼리 구문을 통해 베이스 모델로 <mark style="background-color:#E9D7FD">SBERTKo</mark> 모델을 사용할 것을 명시합니다.</li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문을 통해 모델 생성에 사용할 옵션을 지정합니다.
        <ul>
            <li>"text_col" : 데이터 테이블에서 영화 리뷰 데이터를 담은 컬럼</li>
            <li>"epochs" : 텍스트 수치화 모델을 생성하기 위한 데이터 세트 학습 횟수 (int, DEFAULT : 1)</li>
            <li>"batch_size" : 한 번의 예측에서 읽는 데이터 세트 묶음의 크기 (int, DEFAULT : 16)</li> 
            <li>"learning_rate" : 모델의 학습률 (float, DEFAULT : 3e-5)</li> 
            <li>"train" : False일 경우 사전훈련된 모델을 추가로 학습하지 않고 그대로 사용 (True|False, DEFAULT : True)</li> 
            <li>"overwrite" : 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무 설정. True일 경우 기존 모델은 새로운 모델로 변경됨 (True|False, DEFAULT : False)</li>
        </ul>
        </li>
    </ul>
</div>

다음 "__CONVERT USING__ " 쿼리 구문을 실행하여 `nsmc_test` 텍스트 데이터들을 수치화 합니다. 수치화 된 결과는 `nsmc_test` 테이블에 <mark style="background-color:#D7D0FF ">nsmc_text_search_model_sbertko</mark>이라는 새로운 이름의 컬럼에 저장됩니다.


```python
%%thanosql
CONVERT USING nsmc_text_search_model
OPTIONS (
    text_col="document",
    table_name="nsmc_test",
    batch_size=32
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
      <th>nsmc_text_search_model_sbertko</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>9181084</td>
      <td>꼭 한번 봐야하는 영화인 것 같네요 ㅎㅎ</td>
      <td>1</td>
      <td>[-0.0025036908, 0.014164618200000001, -0.03334...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>6318649</td>
      <td>권선징악은안드로메다로~럽라인은작가빙의된니끼한조연과~여주는남자갖고논질나쁜여자</td>
      <td>0</td>
      <td>[-0.0469921716, 0.021879648800000002, -0.04145...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>5718332</td>
      <td>유쾌하고 신나는 스피드 코미디.</td>
      <td>1</td>
      <td>[-0.0345459133, -0.0036011660000000003, 0.0255...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>8063675</td>
      <td>정말 따뜻했던 드라마ㅠㅠ</td>
      <td>1</td>
      <td>[0.0196540952, 0.0269766878, -0.0278759487, 0....</td>
    </tr>
    <tr>
      <th>4</th>
      <td>10229366</td>
      <td>솔직히 배우들의 연기는 나름 괜찮았다 하지만 냉정하게 영화자체만을 놓고보면 별다른 ...</td>
      <td>0</td>
      <td>[-0.0015408697, 0.00349849, 0.0679392964, 0.06...</td>
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
      <td>[-0.0237155128, 0.0137790618, -0.0214187670000...</td>
    </tr>
    <tr>
      <th>1996</th>
      <td>4584773</td>
      <td>빛돌을 이길 5명의 용사들의 패배</td>
      <td>1</td>
      <td>[-0.0417196229, 0.0020442184, 0.0088670468, 0....</td>
    </tr>
    <tr>
      <th>1997</th>
      <td>2559484</td>
      <td>빌머레이의 굳은 표정에 모든것이 살아있다.. 짐자무시 최고!</td>
      <td>1</td>
      <td>[-0.0065069096, 0.049360271500000004, -0.07191...</td>
    </tr>
    <tr>
      <th>1998</th>
      <td>9867081</td>
      <td>이런영화에 투자하지 않는것이 진정한 한국영화의 발전을위한 초석</td>
      <td>0</td>
      <td>[0.0038481215, 0.056959379500000004, 0.0092997...</td>
    </tr>
    <tr>
      <th>1999</th>
      <td>5984181</td>
      <td>처음부터 끝까지 제가 3번은 더 돌려봤어요. ㅋ 아,초한지여파로 풍소봉 한국방문좀ㅋ</td>
      <td>1</td>
      <td>[-0.0607499145, -0.029679697, -0.055813767, 0....</td>
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
            <li>"text_col" : 데이터 테이블에서 영화 리뷰 데이터를 담은 컬럼</li>
            <li>"table_name" : ThanoSQL DB 내에 저장될 테이블 이름을 정의합니다.</li>
            <li>"batch_size" : 한 번의 예측에서 읽는 데이터 세트 묶음의 크기</li> 
        </ul>
        </li>
    </ul>
</div>

## __3. 텍스트 수치화 모델을 사용해서 유사한 문서 검색하기__

이번 단계에서는 <mark style="background-color:#E9D7FD">nsmc_text_search_model</mark> 텍스트 수치화 모델과 테스트 테이블을 사용하여 유사한 문서를 검색합니다.


```python
%%thanosql
SELECT document, label, nsmc_text_search_model_sbertko_similarity1 as score
FROM (
    SEARCH TEXT text="영화 내용이 불만족스러웠다"
    USING nsmc_text_search_model
    AS 
    SELECT * 
    FROM nsmc_test
    )
ORDER BY score DESC 
LIMIT 10
```

    Searching...





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
      <td>왕건 후속편으로써 매우 실망스런 작품.</td>
      <td>0</td>
      <td>0.609757</td>
    </tr>
    <tr>
      <th>1</th>
      <td>너무 작위적인 스토리 전개..실망스러운 영화ㅠ</td>
      <td>0</td>
      <td>0.608653</td>
    </tr>
    <tr>
      <th>2</th>
      <td>영화 전개가 너무 지루해요.</td>
      <td>0</td>
      <td>0.592399</td>
    </tr>
    <tr>
      <th>3</th>
      <td>시나리오는 한없이 후진데다가 썩은 동선이 가득한 영화</td>
      <td>0</td>
      <td>0.590799</td>
    </tr>
    <tr>
      <th>4</th>
      <td>모든것이 흥미로운 영화였다.</td>
      <td>1</td>
      <td>0.590245</td>
    </tr>
    <tr>
      <th>5</th>
      <td>무관심했던 나를 반성케 한 영화</td>
      <td>1</td>
      <td>0.589808</td>
    </tr>
    <tr>
      <th>6</th>
      <td>영화로서는 많이 아쉬운 듯..</td>
      <td>0</td>
      <td>0.577776</td>
    </tr>
    <tr>
      <th>7</th>
      <td>영화보는 내내 두려움에 떨어 울기만 했다.</td>
      <td>1</td>
      <td>0.577545</td>
    </tr>
    <tr>
      <th>8</th>
      <td>영화가 왜 망한줄 알겠다...취지는 좋았으나 남자주연의 연기력보다 답답한전개가 보는...</td>
      <td>0</td>
      <td>0.577423</td>
    </tr>
    <tr>
      <th>9</th>
      <td>내가 생각했던 스토리와 조금 달랐지만 신선하고 독특했던 영화였다.</td>
      <td>1</td>
      <td>0.573440</td>
    </tr>
  </tbody>
</table>
</div>




```python
%%thanosql
SELECT document, label, nsmc_text_search_model_sbertko_similarity1 as score
FROM (
    SEARCH TEXT text="기분이 좋아지는 작품"
    USING nsmc_text_search_model
    AS 
    SELECT * 
    FROM nsmc_test
    )
ORDER BY score DESC 
LIMIT 10
```

    Searching...





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
      <td>0.747924</td>
    </tr>
    <tr>
      <th>1</th>
      <td>감동적인 영화</td>
      <td>1</td>
      <td>0.694330</td>
    </tr>
    <tr>
      <th>2</th>
      <td>몰입도 잘되고 좋은 영화</td>
      <td>1</td>
      <td>0.681391</td>
    </tr>
    <tr>
      <th>3</th>
      <td>너무 멋있는 영화. 환한 느낌을 준다</td>
      <td>1</td>
      <td>0.668789</td>
    </tr>
    <tr>
      <th>4</th>
      <td>볼 때마다 좋고 또 좋은 영화</td>
      <td>1</td>
      <td>0.651667</td>
    </tr>
    <tr>
      <th>5</th>
      <td>크리스마스의 의미를 다시 생각하게 해 주는 영화</td>
      <td>1</td>
      <td>0.644034</td>
    </tr>
    <tr>
      <th>6</th>
      <td>재밋네요</td>
      <td>1</td>
      <td>0.642170</td>
    </tr>
    <tr>
      <th>7</th>
      <td>너무 재밌다</td>
      <td>1</td>
      <td>0.638125</td>
    </tr>
    <tr>
      <th>8</th>
      <td>오래됐지만 재미있는 영화</td>
      <td>1</td>
      <td>0.634356</td>
    </tr>
    <tr>
      <th>9</th>
      <td>심영이후 감동적인 작품</td>
      <td>1</td>
      <td>0.633446</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>SEARCH TEXT [images|audio|videos|texts|keywords]</strong>" 쿼리 구문은 검색하고자 하는 이미지|오디오|비디오|텍스트|키워드 데이터를 정의합니다.</li>
        <li>"<strong>USING</strong>"은 텍스트 수치화에 사용할 모델을 정의합니다.</li>
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

    Searching...





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
      <th>nsmc_text_search_model_sbertko</th>
      <th>keyword</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>7928591</td>
      <td>일단 엠씨 역량 부족이 가장 큰 문제인듯. 시간 없다고, 재미없다고 게스트 말 끊는...</td>
      <td>0</td>
      <td>[0.0044312407, 0.0219099261, -0.0135033969, 0....</td>
      <td>{'keyword': ['라고 끝 엠씨가노답임', '왜 ᆷ', '시간 재미없', '...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>8380896</td>
      <td>방금 슬쩍 봤는데 '인삼' 처럼 생긴 '산삼' 암수가 서로 토닥이다가 주인공에게 잡...</td>
      <td>0</td>
      <td>[-0.0458624698, 0.0167938117, -0.0452277884, 0...</td>
      <td>{'keyword': ['슬쩍 는데 인삼', '산삼 암 수', '주인공 잡히 ᆷ',...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>8914833</td>
      <td>신세계에 비교하는 거 진심 빡친다. 신성모독</td>
      <td>1</td>
      <td>[-0.020766731400000002, 0.0201531816, -0.03560...</td>
      <td>{'keyword': ['비교 거 진심', '빡 치 신성모독', '신성모독', '진...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>9702804</td>
      <td>킬링타임 최고다 열자채워</td>
      <td>1</td>
      <td>[-0.0018046333, 0.0133265341, -0.0711688772000...</td>
      <td>{'keyword': ['킬링타임 최고', '킬링타임 최고 다', '킬링타임', '...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>10097178</td>
      <td>명불허전 선동영화 10자</td>
      <td>0</td>
      <td>[-0.0208733249, 0.007486234400000001, 0.017354...</td>
      <td>{'keyword': ['명불허전 선동 영화', '명불허전 선동', '명불허전', ...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>9245590</td>
      <td>결말이 마음에 든다. 소설을 먼저 읽었던 터라 결말이 마음에 든다. 세상의 모든 정...</td>
      <td>1</td>
      <td>[0.0214114189, -0.0046590543, 0.0190236121, 0....</td>
      <td>{'keyword': ['결말 마음 들', '힘내 어요', '먼저 읽', '세상 모...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>4227156</td>
      <td>기대했는데.. 시나리오는 ...어떻게 결말이 그렇지?..요즘 막장 드라마 영향인가..</td>
      <td>0</td>
      <td>[0.033407770100000005, -0.0376257747, 0.071577...</td>
      <td>{'keyword': ['는데 시나리오 어떻', '드라마 영향 ᆫ가', '막장 드라...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>7313784</td>
      <td>인기연예인 몇명 출현한다고 재미있는 영화냐 잡것들아긴급조치19호가 그렇게 만들고 망...</td>
      <td>0</td>
      <td>[0.0038064187, 0.007869543500000001, 0.0363081...</td>
      <td>{'keyword': ['영화 냐 잡것', '출현 ᆫ다고 재미있', '인기 연예인 ...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>3582981</td>
      <td>좋았음!!</td>
      <td>1</td>
      <td>[0.0592639893, 0.016511417900000002, -0.036101...</td>
      <td>{'keyword': ['좋 음', '좋', '음'], 'score': [0.535...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>9906269</td>
      <td>멜로와 스릴러를 이렇게 조합할수 있다니 ...</td>
      <td>1</td>
      <td>[-0.0181382429, -0.033325471, 0.02326511030000...</td>
      <td>{'keyword': ['멜로 스릴러', '스릴러 이렇 조합', '멜로', '조합'...</td>
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

    Searching...





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
      <td>[번 영화 네요, 영화 네요, 꼭 번 영화, 꼭, 영화]</td>
      <td>[0.6714, 0.6557000000000001, 0.5977, 0.4049000...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>권선징악은안드로메다로~럽라인은작가빙의된니끼한조연과~여주는남자갖고논질나쁜여자</td>
      <td>0</td>
      <td>[권선징악 안드로메다 ~럽, 조연 ~여주 남자, 작가 빙의 니, 니 끼 조연, 남자...</td>
      <td>[0.5354, 0.45170000000000005, 0.3734, 0.359500...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>유쾌하고 신나는 스피드 코미디.</td>
      <td>1</td>
      <td>[스피드 코미디, 유쾌, 유쾌 신 나, 스피드, 신 나 스피드]</td>
      <td>[0.7241000000000001, 0.6556000000000001, 0.565...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>정말 따뜻했던 드라마ㅠㅠ</td>
      <td>1</td>
      <td>[정말 따뜻하 드라마, 따뜻하 드라마, 따뜻하, 정말, 드라마]</td>
      <td>[0.8292, 0.7047, 0.5895, 0.5657, 0.4277]</td>
    </tr>
    <tr>
      <th>4</th>
      <td>솔직히 배우들의 연기는 나름 괜찮았다 하지만 냉정하게 영화자체만을 놓고보면 별다른 ...</td>
      <td>0</td>
      <td>[연출력 그냥 똥, 배우 연기 나름, 나름 괜찮 하지만, 가능 뻔 전개, 차라리]</td>
      <td>[0.4677, 0.39430000000000004, 0.35250000000000...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>느와르에 멜로에 신파극까지······. 짬뽕이 아니라 꿀꿀이죽</td>
      <td>0</td>
      <td>[느와르 멜로 신파극, 신파극 짬뽕 꿀꿀이죽, 신파극 짬뽕, 느와르, 짬뽕 꿀꿀이죽]</td>
      <td>[0.5616, 0.5278, 0.48190000000000005, 0.3937, ...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>다시 보고 싶네요~</td>
      <td>1</td>
      <td>[다시 싶 네요, 싶 네요, 다시 싶, 네요, 다시]</td>
      <td>[0.7776000000000001, 0.6399, 0.6238, 0.5962000...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>짱이야... 진짜 극장을 나오는데 멋진 소설을 한권 읽은 듯한 기분을 느낌. 재미는...</td>
      <td>1</td>
      <td>[넘치 영화 꼭, 짱 야 진짜, 재미 물론 감동, 권 읽 듯, 느끼 ᆷ]</td>
      <td>[0.5375, 0.5186000000000001, 0.393900000000000...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>유일하게 아들과 같이볼수있는 만화영화</td>
      <td>1</td>
      <td>[만화 영화, 같이 있 만화, 유일 아들 같이, 아들, 유일]</td>
      <td>[0.612, 0.5442, 0.5348, 0.425, 0.3373]</td>
    </tr>
    <tr>
      <th>9</th>
      <td>남자라면 봐라 꼭봐라 두번봐라 세번봐라 계속봐라</td>
      <td>1</td>
      <td>[보 어라 꼭, 세 번, 번 어라 계속, 계속, 남자 라면]</td>
      <td>[0.45430000000000004, 0.4304, 0.4022, 0.361100...</td>
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

    Searching...





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
      <td>번 영화 네요</td>
      <td>0.6714</td>
    </tr>
    <tr>
      <th>1</th>
      <td>꼭 한번 봐야하는 영화인 것 같네요 ㅎㅎ</td>
      <td>1</td>
      <td>영화 네요</td>
      <td>0.6557</td>
    </tr>
    <tr>
      <th>2</th>
      <td>꼭 한번 봐야하는 영화인 것 같네요 ㅎㅎ</td>
      <td>1</td>
      <td>꼭 번 영화</td>
      <td>0.5977</td>
    </tr>
    <tr>
      <th>3</th>
      <td>권선징악은안드로메다로~럽라인은작가빙의된니끼한조연과~여주는남자갖고논질나쁜여자</td>
      <td>0</td>
      <td>권선징악 안드로메다 ~럽</td>
      <td>0.5354</td>
    </tr>
    <tr>
      <th>4</th>
      <td>유쾌하고 신나는 스피드 코미디.</td>
      <td>1</td>
      <td>스피드 코미디</td>
      <td>0.7241</td>
    </tr>
    <tr>
      <th>5</th>
      <td>유쾌하고 신나는 스피드 코미디.</td>
      <td>1</td>
      <td>유쾌</td>
      <td>0.6556</td>
    </tr>
    <tr>
      <th>6</th>
      <td>유쾌하고 신나는 스피드 코미디.</td>
      <td>1</td>
      <td>유쾌 신 나</td>
      <td>0.5653</td>
    </tr>
    <tr>
      <th>7</th>
      <td>유쾌하고 신나는 스피드 코미디.</td>
      <td>1</td>
      <td>스피드</td>
      <td>0.5139</td>
    </tr>
    <tr>
      <th>8</th>
      <td>정말 따뜻했던 드라마ㅠㅠ</td>
      <td>1</td>
      <td>정말 따뜻하 드라마</td>
      <td>0.8292</td>
    </tr>
    <tr>
      <th>9</th>
      <td>정말 따뜻했던 드라마ㅠㅠ</td>
      <td>1</td>
      <td>따뜻하 드라마</td>
      <td>0.7047</td>
    </tr>
    <tr>
      <th>10</th>
      <td>정말 따뜻했던 드라마ㅠㅠ</td>
      <td>1</td>
      <td>따뜻하</td>
      <td>0.5895</td>
    </tr>
    <tr>
      <th>11</th>
      <td>정말 따뜻했던 드라마ㅠㅠ</td>
      <td>1</td>
      <td>정말</td>
      <td>0.5657</td>
    </tr>
    <tr>
      <th>12</th>
      <td>느와르에 멜로에 신파극까지······. 짬뽕이 아니라 꿀꿀이죽</td>
      <td>0</td>
      <td>느와르 멜로 신파극</td>
      <td>0.5616</td>
    </tr>
    <tr>
      <th>13</th>
      <td>느와르에 멜로에 신파극까지······. 짬뽕이 아니라 꿀꿀이죽</td>
      <td>0</td>
      <td>신파극 짬뽕 꿀꿀이죽</td>
      <td>0.5278</td>
    </tr>
    <tr>
      <th>14</th>
      <td>다시 보고 싶네요~</td>
      <td>1</td>
      <td>다시 싶 네요</td>
      <td>0.7776</td>
    </tr>
    <tr>
      <th>15</th>
      <td>다시 보고 싶네요~</td>
      <td>1</td>
      <td>싶 네요</td>
      <td>0.6399</td>
    </tr>
    <tr>
      <th>16</th>
      <td>다시 보고 싶네요~</td>
      <td>1</td>
      <td>다시 싶</td>
      <td>0.6238</td>
    </tr>
    <tr>
      <th>17</th>
      <td>다시 보고 싶네요~</td>
      <td>1</td>
      <td>네요</td>
      <td>0.5962</td>
    </tr>
    <tr>
      <th>18</th>
      <td>다시 보고 싶네요~</td>
      <td>1</td>
      <td>다시</td>
      <td>0.5352</td>
    </tr>
    <tr>
      <th>19</th>
      <td>짱이야... 진짜 극장을 나오는데 멋진 소설을 한권 읽은 듯한 기분을 느낌. 재미는...</td>
      <td>1</td>
      <td>넘치 영화 꼭</td>
      <td>0.5375</td>
    </tr>
    <tr>
      <th>20</th>
      <td>짱이야... 진짜 극장을 나오는데 멋진 소설을 한권 읽은 듯한 기분을 느낌. 재미는...</td>
      <td>1</td>
      <td>짱 야 진짜</td>
      <td>0.5186</td>
    </tr>
    <tr>
      <th>21</th>
      <td>유일하게 아들과 같이볼수있는 만화영화</td>
      <td>1</td>
      <td>만화 영화</td>
      <td>0.6120</td>
    </tr>
    <tr>
      <th>22</th>
      <td>유일하게 아들과 같이볼수있는 만화영화</td>
      <td>1</td>
      <td>같이 있 만화</td>
      <td>0.5442</td>
    </tr>
    <tr>
      <th>23</th>
      <td>유일하게 아들과 같이볼수있는 만화영화</td>
      <td>1</td>
      <td>유일 아들 같이</td>
      <td>0.5348</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>SEARCH KEYWORD [images|audio|videos|texts|keywords]</strong>" 쿼리 구문은 검색하고자 하는 이미지|오디오|비디오|텍스트|키워드 데이터를 정의합니다.</li>
        <li>"<strong>USING</strong>"은 텍스트 수치화에 사용할 모델을 정의합니다.</li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문을 통해 텍스트 수치화 시 필요한 변수들을 정의합니다.
            <ul>
                <li>"lang" : en, ko</li>
                <li>"text_col" : 텍스트가 있는 열 이름</li>
                <li>"ngram_range" : 키워드의 최소 단어 수와 최대 단어수. 예) [1, 3]. 대부분의 상황에서 키워드는 최대 단어 수에 맞춰 추출됩니다 (list[int, int], DEFAULT : [1, 2])</li>
                <li>"top_n" : 추출할 키워드의 수, 유사도가 높은 순서대로 (int, DEFAULT : 5)</li>
                <li>"diversity" : 추출될 키워드의 다양성. 높을 수록 기존에 추출된 키워드와 유사한 키워드는 다시 추출되지 않습니다. 0 <= diversity <= 1 (float, DEFAULT : 0.5)</li>
                <li>"use_stopwords" : 큰 의미가 없는 단어(불용어)를 제외할 지 여부 (True|False, DEFAULT : True)</li>
                <li>"threshold" : 추출할 키워드의 유사도 수치의 최소값 (float, DEFAULT : 0.0)</li>
            </ul>
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
    SELECT document, label, nsmc_text_search_model_sbertko_similarity1 as score
    FROM (
        SEARCH TEXT text="가볍게 볼 수 있는 코미디 영화"
        USING nsmc_text_search_model
        AS 
        SELECT * 
        FROM nsmc_test
        )
    ORDER BY score DESC 
    LIMIT 10
)
```

    Searching...
    Searching...





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
      <td>0.715348</td>
      <td>{'keyword': ['참으로 재밌 영화', '참으로', '영화', '순수', '...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>코미디로도 사람의 마음을 울릴수 있느 영화</td>
      <td>1</td>
      <td>0.667823</td>
      <td>{'keyword': ['코미디 마음 울리', '울리 느 영화', '영화', '코미...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>보고나면 기분 좋은 영화</td>
      <td>1</td>
      <td>0.659354</td>
      <td>{'keyword': ['기분 좋 영화', '좋 영화', '나 기분 좋', '영화'...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>감동적인 영화</td>
      <td>1</td>
      <td>0.657790</td>
      <td>{'keyword': ['감동 영화', '감동', '영화'], 'score': [0...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>시간가는줄 모르고 잼있게봤다 볼만한 영화</td>
      <td>1</td>
      <td>0.657115</td>
      <td>{'keyword': ['보 만 영화', '영화', '잼', '시간 가 줄', '줄...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>긴박한 영화만 좋아한다면 보지말구 영화 자체를 즐기는 사람이라면 감탄할 영화</td>
      <td>1</td>
      <td>0.648480</td>
      <td>{'keyword': ['영화 좋아하 ᆫ다면', '영화 자체', '감탄 영화', '...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>간만에 본 한국영화 중 수작중의 수작</td>
      <td>1</td>
      <td>0.638841</td>
      <td>{'keyword': ['간만에 한국 영화', '한국 영화 수작', '영화 수작 수...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>영화보다 영화 포스터가 더 볼만하군.</td>
      <td>0</td>
      <td>0.626150</td>
      <td>{'keyword': ['영화 보다 영화', '영화 포스터 만', '영화 포스터',...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>오래됐지만 재미있는 영화</td>
      <td>1</td>
      <td>0.626005</td>
      <td>{'keyword': ['오래되 재미있 영화', '재미있 영화', '오래되 재미있'...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>레전드란 말이 어울리는 몇 안되는 영화중 하나</td>
      <td>1</td>
      <td>0.622060</td>
      <td>{'keyword': ['몇 안 영화', '레전드 란', '영화 하나', '란 어울...</td>
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
        SELECT document, label, nsmc_text_search_model_sbertko_similarity1 as score
        FROM (
            SEARCH TEXT text="가볍게 볼 수 있는 코미디 영화"
            USING nsmc_text_search_model
            AS 
            SELECT * 
            FROM nsmc_test
            )
        ORDER BY score DESC 
        LIMIT 10
    )
)
```

    Searching...
    Searching...





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
      <td>[참으로 재밌 영화, 참으로, 영화, 순수, 보]</td>
      <td>[0.7697, 0.5007, 0.5003000000000001, 0.4745000...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>코미디로도 사람의 마음을 울릴수 있느 영화</td>
      <td>1</td>
      <td>[코미디 마음 울리, 울리 느 영화, 영화, 코미디, 느]</td>
      <td>[0.6345000000000001, 0.5515, 0.4253, 0.3686000...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>보고나면 기분 좋은 영화</td>
      <td>1</td>
      <td>[기분 좋 영화, 좋 영화, 나 기분 좋, 영화, 나 기분]</td>
      <td>[0.8464, 0.7501, 0.673, 0.5657, 0.560800000000...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>감동적인 영화</td>
      <td>1</td>
      <td>[감동 영화, 감동, 영화]</td>
      <td>[0.9156000000000001, 0.773, 0.6374000000000001]</td>
    </tr>
    <tr>
      <th>4</th>
      <td>시간가는줄 모르고 잼있게봤다 볼만한 영화</td>
      <td>1</td>
      <td>[보 만 영화, 영화, 잼, 시간 가 줄, 줄]</td>
      <td>[0.45830000000000004, 0.39780000000000004, 0.3...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>긴박한 영화만 좋아한다면 보지말구 영화 자체를 즐기는 사람이라면 감탄할 영화</td>
      <td>1</td>
      <td>[영화 좋아하 ᆫ다면, 영화 자체, 감탄 영화, 긴박 영화, 즐기 라면 감탄]</td>
      <td>[0.6105, 0.5177, 0.44270000000000004, 0.411900...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>간만에 본 한국영화 중 수작중의 수작</td>
      <td>1</td>
      <td>[간만에 한국 영화, 한국 영화 수작, 영화 수작 수작, 수작 수작, 영화]</td>
      <td>[0.719, 0.7169, 0.6803, 0.5345, 0.4847]</td>
    </tr>
    <tr>
      <th>7</th>
      <td>영화보다 영화 포스터가 더 볼만하군.</td>
      <td>0</td>
      <td>[영화 보다 영화, 영화 포스터 만, 영화 포스터, 포스터 만 군, 보다]</td>
      <td>[0.7096, 0.6289, 0.6099, 0.4711, 0.3936]</td>
    </tr>
    <tr>
      <th>8</th>
      <td>오래됐지만 재미있는 영화</td>
      <td>1</td>
      <td>[오래되 재미있 영화, 재미있 영화, 오래되 재미있, 오래되, 영화]</td>
      <td>[0.7341000000000001, 0.644, 0.6168, 0.5843, 0....</td>
    </tr>
    <tr>
      <th>9</th>
      <td>레전드란 말이 어울리는 몇 안되는 영화중 하나</td>
      <td>1</td>
      <td>[몇 안 영화, 레전드 란, 영화 하나, 란 어울리 몇, 하나]</td>
      <td>[0.6089, 0.582, 0.5288, 0.4345, 0.417500000000...</td>
    </tr>
  </tbody>
</table>
</div>




```python
%%thanosql
SELECT document, label, json_array_elements(keyword -> 'keyword') AS keywords, (json_array_elements(keyword -> 'score'))::text::float AS score
FROM (
    SEARCH KEYWORD
    USING nsmc_text_search_model
    OPTIONS (
        text_col="document",
        ngram_range=[1, 3],
        use_stopwords=True
        )
    AS (
        SELECT document, label, nsmc_text_search_model_sbertko_similarity1 as score
        FROM (
            SEARCH TEXT text="최고의 액션 영화"
            USING nsmc_text_search_model
            AS 
            SELECT * 
            FROM nsmc_test
            WHERE document LIKE '%%판타지%%'
            )
        ORDER BY score DESC 
        LIMIT 10
    )
)
WHERE score > 0.3
```

    Searching...
    Searching...





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
      <td>판타지 모험 영화</td>
      <td>0.6236</td>
    </tr>
    <tr>
      <th>1</th>
      <td>정말 재밌다.이런게 바로 판타지모험영화의 현실이다.</td>
      <td>1</td>
      <td>이런 바로 판타지</td>
      <td>0.6016</td>
    </tr>
    <tr>
      <th>2</th>
      <td>정말 재밌다.이런게 바로 판타지모험영화의 현실이다.</td>
      <td>1</td>
      <td>영화 현실</td>
      <td>0.5335</td>
    </tr>
    <tr>
      <th>3</th>
      <td>정말 재밌다.이런게 바로 판타지모험영화의 현실이다.</td>
      <td>1</td>
      <td>정말</td>
      <td>0.4589</td>
    </tr>
    <tr>
      <th>4</th>
      <td>정말 재밌다.이런게 바로 판타지모험영화의 현실이다.</td>
      <td>1</td>
      <td>재밌 이런 바로</td>
      <td>0.4460</td>
    </tr>
    <tr>
      <th>5</th>
      <td>30년 전의 퓨전 판타지라니..그냥 찬양해야됨</td>
      <td>1</td>
      <td>퓨전 판타지 라니</td>
      <td>0.6303</td>
    </tr>
    <tr>
      <th>6</th>
      <td>30년 전의 퓨전 판타지라니..그냥 찬양해야됨</td>
      <td>1</td>
      <td>그냥 찬양 ᆷ</td>
      <td>0.5956</td>
    </tr>
    <tr>
      <th>7</th>
      <td>30년 전의 퓨전 판타지라니..그냥 찬양해야됨</td>
      <td>1</td>
      <td>라니 그냥</td>
      <td>0.4827</td>
    </tr>
    <tr>
      <th>8</th>
      <td>30년 전의 퓨전 판타지라니..그냥 찬양해야됨</td>
      <td>1</td>
      <td>찬양</td>
      <td>0.4442</td>
    </tr>
    <tr>
      <th>9</th>
      <td>30년 전의 퓨전 판타지라니..그냥 찬양해야됨</td>
      <td>1</td>
      <td>30 전</td>
      <td>0.3131</td>
    </tr>
    <tr>
      <th>10</th>
      <td>1700만 1700만 해되서 봤다가 욕만하며 참고 참으며 보다 결국 사분의 일은 스...</td>
      <td>0</td>
      <td>삼류 판타지 아류작</td>
      <td>0.3843</td>
    </tr>
    <tr>
      <th>11</th>
      <td>1700만 1700만 해되서 봤다가 욕만하며 참고 참으며 보다 결국 사분의 일은 스...</td>
      <td>0</td>
      <td>으며 보다 결국</td>
      <td>0.3610</td>
    </tr>
    <tr>
      <th>12</th>
      <td>1700만 1700만 해되서 봤다가 욕만하며 참고 참으며 보다 결국 사분의 일은 스...</td>
      <td>0</td>
      <td>스킵 영화 정말</td>
      <td>0.3280</td>
    </tr>
    <tr>
      <th>13</th>
      <td>1700만 1700만 해되서 봤다가 욕만하며 참고 참으며 보다 결국 사분의 일은 스...</td>
      <td>0</td>
      <td>충무공 부끄럽 지경</td>
      <td>0.2790</td>
    </tr>
    <tr>
      <th>14</th>
      <td>1700만 1700만 해되서 봤다가 욕만하며 참고 참으며 보다 결국 사분의 일은 스...</td>
      <td>0</td>
      <td>1700 1700 해</td>
      <td>0.2613</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>SEARCH TEXT [images|audio|videos|texts|keywords]</strong>" 쿼리 구문은 검색하고자 하는 이미지|오디오|비디오|텍스트|키워드 데이터를 정의합니다.</li>
        <li>"<strong>SEARCH KEYWORD [images|audio|videos|texts|keywords]</strong>" 쿼리 구문은 검색하고자 하는 이미지|오디오|비디오|텍스트|키워드 데이터를 정의합니다.</li>
        <li>"<strong>USING</strong>"은 텍스트 수치화에 사용할 모델을 정의합니다.</li>
        <li>"<strong>AS</strong>" 쿼리 구문은 검색에 사용할 임베딩 테이블을 정의합니다. <code>nsmc_test</code> 테이블을 사용합니다.</li>
    </ul>
</div>

## __6. 튜토리얼을 마치며__

이번 튜토리얼에서는 `nsmc` 영화 리뷰 데이터를 사용하여 텍스트 수치화와 수치화 결과를 바탕으로 한 유사 텍스트 검색, 키워드 추출을 진행해 보았습니다. 이번 튜토리얼에서는 모델의 수치화 성능보다는 작동 위주의 설명으로 진행하였습니다. 나만의 텍스트 수치화모델을 만들어 다양한 형태의 비정형 데이터 세트에 검색 기능을 추가하고 Auto-ML 기법을 이용한 나만의 모델을 배포할 수 있습니다.
<br>
다음 단계에서 텍스트 수치화 모델의 다양한 "__OPTIONS__" 쿼리 구문과 학습 방법을 더욱 심도있게 다뤄봅니다. 나만의 정확한 텍스트 수치화 모델 구축방법에 대해 더욱 자세히 알고 싶다면 다음 튜토리얼들을 진행 해보세요.

* [나만의 데이터 업로드하기](https://docs.thanosql.ai/getting_started/data_upload/)
* [중급 유사 텍스트 검색 모델 만들기]

<div class="admonition tip">
    <h4 class="admonition-title">나만의 서비스를 위한 모델 배포 관련 문의</h4>
    <p>ThanoSQL을 활용해 나만의 모델을 만들거나, 나의 서비스에 적용하는데 어려움이 있다면 언제든 아래로 문의주세요😊</p>
    <p>유사 텍스트 검색 모델 구축 관련 문의: contact@smartmind.team</p>
</div>
