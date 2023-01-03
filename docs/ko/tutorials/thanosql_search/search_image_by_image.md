---
title: 이미지로 이미지 검색하기
---

# __이미지로 이미지 검색하기__

- 튜토리얼 난이도: ★☆☆☆☆
- 읽는데 걸리는 시간: 7분
- 사용 언어: [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 실행 파일 위치: tutorial/thanosql_search/search_image_by_image.ipynb   
- 참고 문서: [MNIST 데이터 세트 소개](https://ko.wikipedia.org/wiki/MNIST_%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4), [A Simple Framework for Contrastive Learning of Visual Representations](https://arxiv.org/abs/2002.05709)

## 튜토리얼 소개

<div class="admonition note">
    <h4 class="admonition-title">이미지 수치화 기술 이해하기</h4>
    <p>이미지는 고차원 데이터(높이x너비x채널[RGB]x색의 강도)로써 각 픽셀의 정보를 무작위로 생성한다면 아무 의미를 갖지 않는 이미지가 생성됩니다. 즉, 각 픽셀은 주변 픽셀과 연관된 특정 패턴을 갖는 경우에만 사람이 이미지로 인식을 할 수 있게 됩니다. 이는 이미지를 실제보다 저차원적 특성 벡터에 표현할 수 있다는 의미입니다. 최근에는 인공지능을 이용해서 각 이미지가 갖는 의미의 유사도에 따라 저차원 공간에 수치화하여 표현하는 연구들이 다방면으로 이루어지고 있으며 이는 이미지 수치화, 벡터화(Vectorization), 임베딩(Embedding) 등의 이름으로 다양하게 활용되고 있습니다.</p>
</div>

이미지의 유사도를 정의하는 방법은 여러가지가 있습니다. 색상이 비슷하거나, 이미지 내의 사물이 비슷하거나, 손글씨처럼 의미가 동일할 수도 있습니다. 유사한 이미지에 대한 정확한 정의를 내리기 어렵지만 이미지가 보유하고 있는 일반적인 특징을 인공지능은 학습하고 수치화합니다.

ThanoSQL에서는 이미지를 입력하고 데이터베이스에서 유사한 이미지를 검색하기 위해 [자가학습모델(Self-Supervised Learning Model)](https://en.wikipedia.org/wiki/Self-supervised_learning)을 사용합니다. 사용자가 보유하고 있는 이미지들을 ThanoSQL의 데이터베이스에 올리면 인공지능 알고리즘을 통해 비슷한 이미지는 가깝게 다른 이미지들은 멀리 배치하며 스스로 학습을 진행합니다. 정답이 없는 데이터 세트에서 이미지의 일반적인 표현을 학습하고 소량의 목푯값(Target)이 있는 이미지로 미세 조정하여 분류나 회귀 작업에 활용할 수 있습니다. 

또한, ThanoSQL은 인공지능 알고리즘을 이용해서 데이터 세트를 수치화 합니다. 이렇게 수치화 된 데이터는 데이터베이스의 컬럼 내에 저장되고, 이미지 간 유사도(거리) 계산을 통해 비슷한 이미지를 검색하는데 사용됩니다.

__아래는 ThanoSQL 유사 이미지 검색 알고리즘의 활용 및 예시 입니다.__   

- 사용자가 좋아하는 이미지를 입력받으면 데이터베이스 상에 저장되어 있는 미술 작품 중에서 비슷한 느낌의 미술 작품들을 검색하고 사용자에게 추천합니다.
- 수천장의 사진이 담겨있는 앨범에서 유사 이미지를 찾아낼 수 있습니다.
- 이미지의 수치화 된 값을 ThanoSQL의 데이터베이스에 저장하고, ThanoSQL Auto-ML 회귀/분류 예측 모델을 이용해서 나만의 검색 엔진이나 인공지능 모델을 만들 수 있습니다.
 
<div class="admonition note">
    <h4 class="admonition-title">본 튜토리얼에서는</h4>
    <p>👉 이번 튜토리얼에서는 <mark style="background-color:#FFD79C">MNIST 손글씨 데이터 세트</mark>를 사용합니다. 각 이미지는 0~1 사이의 값을 갖는 고정 크기(28x28 = 784 픽셀) 이며, 여러 사람들이 손으로 쓴 0~9까지 숫자를 정답과 함께 제공합니다. 1,000개의 학습용 데이터 세트와 200개의 테스트용 데이터 세트로 이루어져 있습니다.</p>
</div>
    
ThanoSQL을 사용하여 손글씨 데이터를 입력하고 데이터베이스 내에서 입력 이미지와 유사한 이미지를 검색해주는 모델을 만들어 봅니다. 

[![IMAGE](https://docs.thanosql.ai/img/thanosql_search/search_image_by_image/simclr_img7.png "MNIST 데이터")](https://docs.thanosql.ai/img/thanosql_search/search_image_by_image/simclr_img7.png)

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
GET THANOSQL DATASET mnist_data
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
COPY mnist_train 
OPTIONS (overwrite=True)
FROM "thanosql-dataset/mnist_data/mnist_train.csv"
```

    Success



```python
%%thanosql
COPY mnist_test 
OPTIONS (overwrite=True)
FROM "thanosql-dataset/mnist_data/mnist_test.csv"
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

손글씨 분류 모델을 만들기 위해 ThanoSQL 워크스페이스 [데이터베이스](https://ko.wikipedia.org/wiki/%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4)에 저장되어 있는 <mark style="background-color:#FFEC92">mnist_train</mark> 테이블을 사용합니다. <mark style="background-color:#FFEC92">mnist_train</mark> 테이블은 <mark style="background-color:#FFD79C">MNIST</mark> 이미지 파일들이 저장되어 있는 경로와 파일 이름 그리고 라벨 정보가 담겨 있는 테이블입니다. 아래의 쿼리문을 실행하고 테이블의 내용을 확인합니다.


```python
%%thanosql
SELECT * 
FROM mnist_train 
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
      <th>image_path</th>
      <th>filename</th>
      <th>label</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>thanosql-dataset/mnist_data/train/6782.jpg</td>
      <td>6782.jpg</td>
      <td>5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>thanosql-dataset/mnist_data/train/1810.jpg</td>
      <td>1810.jpg</td>
      <td>5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>thanosql-dataset/mnist_data/train/33617.jpg</td>
      <td>33617.jpg</td>
      <td>5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>thanosql-dataset/mnist_data/train/27802.jpg</td>
      <td>27802.jpg</td>
      <td>5</td>
    </tr>
    <tr>
      <th>4</th>
      <td>thanosql-dataset/mnist_data/train/50677.jpg</td>
      <td>50677.jpg</td>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">데이터 테이블 이해하기</h4>
    <p><mark style="background-color:#FFEC92">mnist_train</mark> 테이블은 아래와 같은 정보를 담고 있습니다. "6782.jpg" 이미지 파일은 숫자 5를 쓴 손글씨 이미지입니다.</p>
    <ul>
        <li><mark style="background-color:#D7D0FF">image_path</mark>: 이미지 경로</li>
        <li><mark style="background-color:#D7D0FF">filename</mark>: 파일 이름</li>
        <li><mark style="background-color:#D7D0FF">label</mark>: 이미지 라벨</li>
    </ul>
</div>

## __2. 이미지 수치화 모델 생성__

이전 단계에서 확인한 <mark style="background-color:#FFEC92">mnist_train</mark> 테이블을 사용하여 이미지 수치화 모델을 만듭니다. 아래의 쿼리 구문을 실행하여 <mark style="background-color:#E9D7FD">my_image_search_model</mark>이라는 이름의 모델을 만듭니다.  
(쿼리 실행 시 예상 소요 시간: 1 min)


```python
%%thanosql
BUILD MODEL my_image_search_model
USING SimCLR
OPTIONS (
    image_col="image_path",
    max_epochs=1,
    overwrite=True
    )
AS 
SELECT * 
FROM mnist_train
```

    Success


<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>BUILD MODEL</strong>" 쿼리 구문을 사용하여 <mark style="background-color:#E9D7FD">mnist_model</mark> 이라는 모델을 만들고 학습시킵니다.</li>
        <li>"<strong>USING</strong>" 쿼리 구문을 통해 베이스 모델로 <mark style="background-color:#E9D7FD">SimCLR</mark> 모델을 사용할 것을 명시합니다.</li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문을 통해 모델 생성에 사용할 옵션을 지정합니다.
        <ul>
            <li>"image_col": 데이터 테이블에서 이미지의 경로를 담은 컬럼 (default: "image_path")</li>
            <li>"max_epochs": 이미지 수치화 모델을 생성하기 위한 데이터 세트 학습 횟수</li>
            <li>"overwrite": 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 여부 설정. True일 경우 기존 모델은 새로운 모델로 변경됨 (True|False, default: False) </li>
        </ul>
        </li>
    </ul>
</div>

다음 "__CONVERT USING__ " 쿼리 구문을 실행하여 `mnist_test` 이미지들을 수치화 합니다. 수치화 된 결과는 `mnist_test` 테이블에 사용자가 옵션으로 지정한 이름(default: <mark style="background-color:#D7D0FF ">convert_result</mark>)의 컬럼에 저장됩니다.


```python
%%thanosql
CONVERT USING my_image_search_model
OPTIONS (
    table_name= "mnist_test",
    image_col="image_path",
    column_name="convert_result"
    )
AS 
SELECT * 
FROM mnist_test
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
      <th>image_path</th>
      <th>filename</th>
      <th>label</th>
      <th>convert_result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>thanosql-dataset/mnist_data/test/5099.jpg</td>
      <td>5099.jpg</td>
      <td>6</td>
      <td>[0.35765496, 0.70219374, 1.036354, 0.30916533,...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>thanosql-dataset/mnist_data/test/9239.jpg</td>
      <td>9239.jpg</td>
      <td>6</td>
      <td>[0.31446555, 0.4085992, 0.65440047, 0.3968239,...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>thanosql-dataset/mnist_data/test/2242.jpg</td>
      <td>2242.jpg</td>
      <td>6</td>
      <td>[0.42804983, 0.57595205, 0.87231886, 0.3619018...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>thanosql-dataset/mnist_data/test/3451.jpg</td>
      <td>3451.jpg</td>
      <td>6</td>
      <td>[0.4673535, 0.52365816, 0.8862568, 0.35323358,...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>thanosql-dataset/mnist_data/test/2631.jpg</td>
      <td>2631.jpg</td>
      <td>6</td>
      <td>[0.43351364, 0.6121249, 1.0112104, 0.38044024,...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>195</th>
      <td>thanosql-dataset/mnist_data/test/8045.jpg</td>
      <td>8045.jpg</td>
      <td>8</td>
      <td>[0.6956904, 0.6110612, 0.95983946, 0.6331365, ...</td>
    </tr>
    <tr>
      <th>196</th>
      <td>thanosql-dataset/mnist_data/test/9591.jpg</td>
      <td>9591.jpg</td>
      <td>8</td>
      <td>[0.43570492, 0.4828499, 0.59078306, 0.24152778...</td>
    </tr>
    <tr>
      <th>197</th>
      <td>thanosql-dataset/mnist_data/test/7425.jpg</td>
      <td>7425.jpg</td>
      <td>8</td>
      <td>[0.38205424, 0.39592215, 0.82946587, 0.2115911...</td>
    </tr>
    <tr>
      <th>198</th>
      <td>thanosql-dataset/mnist_data/test/2150.jpg</td>
      <td>2150.jpg</td>
      <td>8</td>
      <td>[0.37463677, 0.9733489, 1.4452345, 0.22567756,...</td>
    </tr>
    <tr>
      <th>199</th>
      <td>thanosql-dataset/mnist_data/test/5087.jpg</td>
      <td>5087.jpg</td>
      <td>8</td>
      <td>[0.318596, 0.78893185, 0.88471705, 0.2650501, ...</td>
    </tr>
  </tbody>
</table>
<p>200 rows × 4 columns</p>
</div>



<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>CONVERT USING</strong>" 쿼리 구문은 <code>my_image_search_model</code>을 이미지 수치화를 위한 알고리즘으로 사용합니다.   </li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문을 통해 이미지 수치화 시 필요한 변수들을 정의합니다.
        <ul>
            <li>"table_name": ThanoSQL 워크스페이스 데이터베이스 내에 저장될 테이블 이름을 정의합니다. </li>
            <li>"image_col": 데이터 테이블에서 이미지의 경로를 담은 컬럼 (default: "image_path")</li>
            <li>"column_name": 데이터 테이블에서 수치화된 결과를 담을 컬럼 이름을 정의합니다. (default: "convert_result")</li>
        </ul>
        </li>
    </ul>
</div>

## __3. 이미지 수치화 모델을 사용해서 유사 이미지 검색하기__

이번 단계에서는 <mark style="background-color:#E9D7FD">my_image_search_model</mark> 이미지 수치화 모델과 테스트 테이블을 사용하여 "923.jpg" 이미지 파일(손글씨 8)과 유사한 이미지를 검색합니다.

<a href="https://docs.thanosql.ai/img/thanosql_search/search_image_by_image/simclr_img8.png">
    <img alt="IMAGE" src="https://docs.thanosql.ai/img/thanosql_search/search_image_by_image/simclr_img8.png" style="width:100px">
</a>

<p style="text-align:center">923.jpg 이미지파일</p>


```python
%%thanosql
SEARCH IMAGE image='thanosql-dataset/mnist_data/test/923.jpg'
USING my_image_search_model
OPTIONS (
    emb_col="convert_result",
    column_name="search_result"
    )
AS
SELECT * 
FROM mnist_test
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
      <th>image_path</th>
      <th>filename</th>
      <th>label</th>
      <th>convert_result</th>
      <th>search_result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>thanosql-dataset/mnist_data/test/5099.jpg</td>
      <td>5099.jpg</td>
      <td>6</td>
      <td>[0.35765496, 0.70219374, 1.036354, 0.30916533,...</td>
      <td>0.976650</td>
    </tr>
    <tr>
      <th>1</th>
      <td>thanosql-dataset/mnist_data/test/9239.jpg</td>
      <td>9239.jpg</td>
      <td>6</td>
      <td>[0.31446555, 0.4085992, 0.65440047, 0.3968239,...</td>
      <td>0.966272</td>
    </tr>
    <tr>
      <th>2</th>
      <td>thanosql-dataset/mnist_data/test/2242.jpg</td>
      <td>2242.jpg</td>
      <td>6</td>
      <td>[0.42804983, 0.57595205, 0.87231886, 0.3619018...</td>
      <td>0.970497</td>
    </tr>
    <tr>
      <th>3</th>
      <td>thanosql-dataset/mnist_data/test/3451.jpg</td>
      <td>3451.jpg</td>
      <td>6</td>
      <td>[0.4673535, 0.52365816, 0.8862568, 0.35323358,...</td>
      <td>0.970680</td>
    </tr>
    <tr>
      <th>4</th>
      <td>thanosql-dataset/mnist_data/test/2631.jpg</td>
      <td>2631.jpg</td>
      <td>6</td>
      <td>[0.43351364, 0.6121249, 1.0112104, 0.38044024,...</td>
      <td>0.975437</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>195</th>
      <td>thanosql-dataset/mnist_data/test/8045.jpg</td>
      <td>8045.jpg</td>
      <td>8</td>
      <td>[0.6956904, 0.6110612, 0.95983946, 0.6331365, ...</td>
      <td>0.972526</td>
    </tr>
    <tr>
      <th>196</th>
      <td>thanosql-dataset/mnist_data/test/9591.jpg</td>
      <td>9591.jpg</td>
      <td>8</td>
      <td>[0.43570492, 0.4828499, 0.59078306, 0.24152778...</td>
      <td>0.964288</td>
    </tr>
    <tr>
      <th>197</th>
      <td>thanosql-dataset/mnist_data/test/7425.jpg</td>
      <td>7425.jpg</td>
      <td>8</td>
      <td>[0.38205424, 0.39592215, 0.82946587, 0.2115911...</td>
      <td>0.969010</td>
    </tr>
    <tr>
      <th>198</th>
      <td>thanosql-dataset/mnist_data/test/2150.jpg</td>
      <td>2150.jpg</td>
      <td>8</td>
      <td>[0.37463677, 0.9733489, 1.4452345, 0.22567756,...</td>
      <td>0.978564</td>
    </tr>
    <tr>
      <th>199</th>
      <td>thanosql-dataset/mnist_data/test/5087.jpg</td>
      <td>5087.jpg</td>
      <td>8</td>
      <td>[0.318596, 0.78893185, 0.88471705, 0.2650501, ...</td>
      <td>0.983770</td>
    </tr>
  </tbody>
</table>
<p>200 rows × 5 columns</p>
</div>



<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>SEARCH IMAGE [image|text|audio|video]</strong>" 쿼리 구문은 검색하고자 하는 이미지|텍스트|오디오|비디오 파일을 정의합니다.</li>
        <li>"<strong>USING</strong>"은 이미지 수치화에 사용할 모델을 정의합니다.</li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문을 통해 이미지 검색 시 필요한 변수들을 정의합니다.
        <ul>
                <li>"emb_col": 데이터 테이블에서 수치화된 결과를 담은 컬럼</li>
                <li>"column_name": 데이터 테이블에서 검색 결과를 담을 컬럼 이름을 정의합니다. (default: "search_result")</li>
        </ul>
        </li>
        <li>"<strong>AS</strong>" 쿼리 구문은 검색에 사용할 임베딩 테이블을 정의합니다. <code>mnist_test</code> 테이블을 사용합니다 </li>
    </ul>
</div>

다음 쿼리를 실행하여 "__SEARCH__" 결과를 ThanoSQL의 "__PRINT__" 쿼리 구문을 활용하여 가장 유사한 상위 4개를 출력합니다. 학습을 조금 밖에 진행하지 않았지만 8과 비슷한 이미지를 출력하는 것을 확인할 수 있습니다.


```python
%%thanosql
PRINT IMAGE 
AS (
    SELECT image_path, search_result 
    FROM (
        SEARCH IMAGE image='thanosql-dataset/mnist_data/test/923.jpg' 
        USING my_image_search_model 
        OPTIONS (
            emb_col="convert_result",
            column_name="search_result"
            )
        AS 
        SELECT * 
        FROM mnist_test
        )
    ORDER BY search_result DESC 
    LIMIT 4
    )
```

    /home/jovyan/thanosql-dataset/mnist_data/test/923.jpg



    
![jpeg](/ko/img/tutorials/thanosql_search/search_image_by_image/output_24_1.jpg)
    


    /home/jovyan/thanosql-dataset/mnist_data/test/5087.jpg



    
![jpeg](/ko/img/tutorials/thanosql_search/search_image_by_image/output_24_3.jpg)
    


    /home/jovyan/thanosql-dataset/mnist_data/test/7645.jpg



    
![jpeg](/ko/img/tutorials/thanosql_search/search_image_by_image/output_24_5.jpg)
    


    /home/jovyan/thanosql-dataset/mnist_data/test/4147.jpg



    
![jpeg](/ko/img/tutorials/thanosql_search/search_image_by_image/output_24_7.jpg)
    


<div class="admonition danger">
    <h4 class="admonition-title">참고 사항</h4>
    <p>이미지 유사도 검색 알고리즘의 기본 학습 옵션은 이미지의 좌우상하 반전, 색상의 변화 등에 관계없이 모두 같은 이미지로 인식하도록 학습이 진행 됩니다. 강아지의 사진은 뒤집히거나 색이 변해도 강아지로 인식되어야 하기 때문입니다. 의류 이미지 등과 같이 색의 변화가 중요하거나 숫자 처럼 상하, 좌우 반전이 중요한 경우 학습 시 옵션을 변경해 주어야 합니다. 본 튜토리얼에서는 이러한 이미지 유사도 검색의 특징을 보여주고 있습니다.</p>
</div>

## __4. 튜토리얼을 마치며__

이번 튜토리얼에서는 `MNIST` 손글씨 데이터 세트를 사용하여 이미지 수치화와 수치화 결과를 바탕으로한 유사 이미지 검색까지 진행해 보았습니다. 이번 튜토리얼에서는 이미지 유사도의 정확도보다도 작동 위주의 설명으로 진행하였습니다. 이미지 수치화 모델은 각 이미지 데이터 세트에 맞는 정밀한 튜닝과 소량의 정답을 학습 시에 추가하여 정확도를 향상 시킬 수 있습니다. 

* [나만의 데이터 업로드하기](https://docs.thanosql.ai/ko/getting_started/data_upload/)
* [나만의 데이터 테이블 생성하기](https://docs.thanosql.ai/ko/how-to_guides/ThanoSQL_query/COPY_SYNTAX/)
* [나만의 모델 업로드하기](https://docs.thanosql.ai/ko/how-to_guides/ThanoSQL_query/UPLOAD_SYNTAX/)

<div class="admonition tip">
    <h4 class="admonition-title">나만의 서비스를 위한 모델 배포 관련 문의</h4>
    <p>ThanoSQL을 활용해 나만의 모델을 만들거나, 나의 서비스에 적용하는데 어려움이 있다면 언제든 아래로 문의주세요😊</p>
    <p>유사 이미지 검색 모델 구축 관련 문의: contact@smartmind.team</p>
</div>
