<<<<<<< Updated upstream
# ThanoSQL Syntax
## (안내 가이드) 쿼리 구문 사용 튜토리얼

**[이전 문서 - ](https://github.com/smartmind-team/thanosql-docs/blob/sunkeun/docs/tutorials/thanosql_ml/tabular/classification/automl_classification.md)** <br>| **[다음 문서 - ](https://github.com/smartmind-team/thanosql-docs/blob/indoo2/docs/tutorials/thanosql_ml/tabular/recommendation/lv1_lfm_kor_0_1.md)**

## 시작 전 사전정보

=======
<<<<<<< HEAD
# ThanoSQL 쿼리 구문 사용 튜토리얼

 * ThanoSQL은 ANSI/ISO 표준 준수하며 85% 이상의 표준 SQL 쿼리문들과 호환합니다.
 * ThanoSQL만의 쿼리기능을 통해 이미지/동영상 내의 내용 검색이 가능합니다.
 * ThanoSQL만의 쿼리기능을 통해 이미지분류모델, 회귀예측, 회귀분류, 추천시스템 등의 인공지능 모델들을 SQL쿼리문으로 쉽고 간단하게 만들 수 있습니다.
 <br>
 
## ThanoSQL 쿼리구문 구조 및 기능 미리보기

|쿼리구문|기능|
=======
# ThanoSQL Syntax
## (안내 가이드) 쿼리 구문 사용 튜토리얼

**[이전 문서 - ](https://github.com/smartmind-team/thanosql-docs/blob/sunkeun/docs/tutorials/thanosql_ml/tabular/classification/automl_classification.md)** <br>| **[다음 문서 - ](https://github.com/smartmind-team/thanosql-docs/blob/indoo2/docs/tutorials/thanosql_ml/tabular/recommendation/lv1_lfm_kor_0_1.md)**

## 시작 전 사전정보

>>>>>>> Stashed changes
- 튜토리얼 난이도 : ★★☆☆☆
- 읽는데 걸리는 시간 : 10분
- 사용 언어 : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 마지막 수정날짜 : 2022-06-01

<br>

## 튜토리얼 소개
ThanoSQL에서 사용하는 문법들의 기능을 모아두었습니다. 각 문법의 사용 방법과 옵션을 제공합니다. 튜토리얼 문서와 함께 활용하면서 나만의 모델을 만들고 배포해 보세요. <br>
 * ThanoSQL은 [ANSI/ISO 표준](https://en.wikipedia.org/wiki/SQL_syntax)을 준수하며 표준 SQL 쿼리 구문들과 대부분 호환 가능합니다.
 * ThanoSQL만의 쿼리 기능을 통해 이미지/동영상 내의 내용 검색이 가능합니다.
 * ThanoSQL만의 쿼리 기능을 통해 이미지 분류 모델, 회귀 예측, 분류 예측, 추천 모델 등의 인공지능 알고리즘들을 SQL 쿼리 구문으로 쉽고 간단하게 만들 수 있습니다. <br><br>
 ![image](https://user-images.githubusercontent.com/50227222/169960904-1e884a17-39d8-404a-bbdb-a7c3450dfb3c.png)

 <br>
 
## ThanoSQL Clauses 미리보기

|Clause|Function|
<<<<<<< Updated upstream
=======
>>>>>>> 6d192ca8c0ec1de429143239776e34ae0e8bb09d
>>>>>>> Stashed changes
|:---|:---|
|BUILD MODEL|모델 빌드|
|FIT MODEL|모델 (재)학습|
|DELETE MODEL|모델 삭제|
<<<<<<< Updated upstream
|TRANSFORM USING|테이블/데이터프레임 형태 변환|
|PREDICT USING|모델을 사용한 예측|
|EVALUATE USING|모델 성능 평가|
|CREATE TABLE|비정형 데이터(이미지, 음성, 영상)를 이용해서 정형테이블을 생성|
|CONVERT|비정형 데이터의 정보를 이용해서 정형 컬럼을 추가|
|SEARCH|비정형 데이터 내용/의미/유사도 검색|
|PRINT|비졍형 데이터 출력|

<br>

## 1. BUILD MODEL Clause

"__BUILD MODEL__" 쿼리 구문은 사용자가 원하는 인공지능 모델을 구축하고 학습시킵니다. 사용자는 데이터 과학(Data Science)에 대한 전문지식이 없어도 간단하게 "__BUILD MODEL__" 쿼리 구문만을 사용하여 인공지능 모델을 개발 할 수 있습니다.

### 1.1 BUILD MODEL Statement

=======
<<<<<<< HEAD
|TRANSFORM|학습 전 데이터셋 정형|
|PREDICT|모델을 사용한 예측|
|EVALUATE|모델 성능 평가|
|CREATE TABLE|비정형데이터(이미지, 음성, 영상)의 임베딩 테이블 생성|
|CONVERT|비정형데이터(이미지, 음성, 영상) 임베딩 (벡터수치화)|
|SEARCH|비정형데이터(이미지, 음성, 영상) 내용 검색|
|PRINT|비졍형데이터(이미지,음성,영상) 출력|

<br>

## 1. BUILD MODEL 쿼리 구문 설명
=======
|TRANSFORM USING|테이블/데이터프레임 형태 변환|
|PREDICT USING|모델을 사용한 예측|
|EVALUATE USING|모델 성능 평가|
|CREATE TABLE|비정형 데이터(이미지, 음성, 영상)를 이용해서 정형테이블을 생성|
|CONVERT|비정형 데이터의 정보를 이용해서 정형 컬럼을 추가|
|SEARCH|비정형 데이터 내용/의미/유사도 검색|
|PRINT|비졍형 데이터 출력|

<br>

## 1. BUILD MODEL Clause
>>>>>>> 6d192ca8c0ec1de429143239776e34ae0e8bb09d

"__BUILD MODEL__" 쿼리 구문은 사용자가 원하는 인공지능 모델을 구축하고 학습시킵니다. 사용자는 데이터 과학(Data Science)에 대한 전문지식이 없어도 간단하게 "__BUILD MODEL__" 쿼리 구문만을 사용하여 인공지능 모델을 개발 할 수 있습니다.

<<<<<<< HEAD
### 1.1 BUILD MODEL 쿼리 구문 기본 구조
=======
### 1.1 BUILD MODEL Statement
>>>>>>> 6d192ca8c0ec1de429143239776e34ae0e8bb09d

>>>>>>> Stashed changes
```python
BUILD MODEL [사용자 지정 모델 이름] 
USING [사용할 베이스 알고리즘]
OPTIONS ([알고리즘 별 모델 빌드에 필요한 파라미터 등의 옵션값])
AS [사용할 데이터 세트] 
```

<<<<<<< Updated upstream
"__OPTIONS__"에서 사용하는 파라미터 등의 옵션값은 사용하는 베이스 알고리즘에 따라 다르게 적용됩니다. 각 알고리즘에 대한 "__OPTIONS__"는 [다음 문서]()에서 확인 가능합니다.

### 1.2 BUILD MODEL Examples
=======
<<<<<<< HEAD
### 1.2 BUILD MODEL 사용 예시

#### 1.2.1 추천 시스템 Light_FM모델 사용
=======
"__OPTIONS__"에서 사용하는 파라미터 등의 옵션값은 사용하는 베이스 알고리즘에 따라 다르게 적용됩니다. 각 알고리즘에 대한 "__OPTIONS__"는 [다음 문서]()에서 확인 가능합니다.

### 1.2 BUILD MODEL Examples
>>>>>>> 6d192ca8c0ec1de429143239776e34ae0e8bb09d
>>>>>>> Stashed changes

#### <div id="1_2_1">1.2.1 추천 모델 구축을 위해 "__Light_FM__" 베이스 알고리즘 사용</div>

아래 예는 "__BUILD MODEL__" 쿼리문을 사용하여 `user_rec`라는 모델을 ["__Light_FM__"]()이라는 베이스 알고리즘을 학습시켜 추천 모델을 구축합니다. 자세한 코드 내용이 궁금하다면 [Movielens 영화평점 데이터를 이용한 영화 추천 모델 만들기](https://github.com/smartmind-team/thanosql-docs/blob/indoo2/docs/tutorials/thanosql_ml/tabular/recommendation/lv1_lfm_kor_0_1.md)를 진행해 보세요.

```python
%%thanosql
BUILD MODEL user_rec
USING Light_FM
OPTIONS (
 user='userid',
 item='movieid',
 target='rating',
 description='title'
 )
AS 
SELECT * 
FROM movielens_train 
```

>### 쿼리 세부정보
<<<<<<< Updated upstream
=======
<<<<<<< HEAD
>```BUILD MODEL``` 쿼리를 사용하여 movie_rec 이라는 모델을 만들고 학습시킵니다.
>```OPTIONS(user_col='userid',item_col='movieid',rating_col='rating', item_names= 'title', ...)``` 쿼리는 모델 생성에 필수적으로 필요한 movielens 샘플데이터셋의 유저칼럼이름, 아이템칼럼이름, 평점칼럼 이름들을 할당하여 데이터셋 전처리 및 모델 빌드가 가능하도록 합니다. 모델의 파라미터 튜닝 옵션들 또한 사용이 가능합니다. Options에 따로 파라미터 할당을 하지 않으면 기본적인 파라미터 값으로 모델이 생성됩니다. 사용가능한 파라미터에 대한 설명은 https://making.lyst.com/lightfm/docs/lightfm.html 를 통해 확인할 수 있습니다.

#### 1.2.2 타이타닉 생존분류 데이터셋 예시
아래 예시는 AutomlClassifier 분류모델을 사용하여 타이타닉 생존여부 예측 모델을 만듭니다. 
=======
>>>>>>> Stashed changes
>"__OPTIONS__"에 따로 파라미터 할당을 하지 않으면 기본적인 파라미터 값으로 모델이 생성됩니다. <br> 베이스 알고리즘의 파라미터 튜닝 옵션들 또한 사용이 가능합니다. 사용가능한 파라미터에 대한 추가적인 설명은 [참조 페이지](https://making.lyst.com/lightfm/docs/lightfm.html)를 통해 확인할 수 있습니다.

#### 1.2.2 타이타닉 생존분류 데이터 세트 예시
아래 예는 ["__AutomlClassifier__"]() 분류모델을 사용하여 자동화 된 분류 예측 모델을 만듭니다. 
<<<<<<< Updated upstream
=======
>>>>>>> 6d192ca8c0ec1de429143239776e34ae0e8bb09d
>>>>>>> Stashed changes
자세한 코드 내용이 궁금하다면 [타이타닉 생존분류 튜토리얼]()을 진행해 보세요.

```python
%%thanosql
BUILD MODEL test_classifier
USING AutomlClassifier
OPTIONS (
 target='survived',
 impute_type='simple',
 features_to_drop=["name", "ticket", "passengerid", "cabin"]
 ) 
AS 
SELECT * 
FROM titanic_train 
LIMIT 100
```

<<<<<<< Updated upstream
:point_right: "__BUILD MODEL__" 쿼리 구문에서 "__USING__"을 사용해 학습이 가능한 베이스 알고리즘의 종류는 다음과 같습니다. 사용할 수 있는 모델은 지속적으로 업데이트 중에 있습니다.
* Auto-ML 분류 모델 - [__AutomlClassifier__]()
* Auto-ML 회귀 모델 - [__AutomlRegressor__]()
* 추천 모델 - [__Light_FM__]()
* 추천 모델 - [__LDAREC__]()


## 2. FIT MODEL Clause
"__FIT MODEL__"은 이미 학습된 모델에 새로운 데이터를 추가하여 재학습 시킬때 사용되는 쿼리 구문 입니다. 최초 학습 이후 데이터가 추가적으로 확보 되어 기존 모델을 업데이트 하거나,  사전학습 된 인공지능 오픈소스 모델을 불러와 나만의 데이터로 재학습 시킬 경우 사용할 수 있습니다.

### 2.1 FIT MODEL Statement

```python
FIT MODEL [사용자 지정 모델 이름]
USING [빌드했던 모델 이름]
OPTIONS ([알고리즘 별 모델 빌드에 필요한 파라미터 등의 옵션값]) 
AS [사용할 데이터 세트]            
```


### 2.2 FIT MODEL Examples
아래 예는 "__FIT MODEL__" 쿼리 구문을 사용하여 이전에 만들었던 `test_classifier` 모델에 새로운 데이터 세트를 추가 학습하여 `fit_test_classifier`라는 모델을 만듭니다. 
=======
<<<<<<< HEAD
## 2. FIT MODEL 쿼리 구문 설명
FIT MODEL은 이미 학습된 모델에 새로운 데이터를 추가하여 재학습 시킬때 사용되는 쿼리구문 입니다.

### 2.1 FIT 쿼리 구문 기본 구조
=======
:point_right: "__BUILD MODEL__" 쿼리 구문에서 "__USING__"을 사용해 학습이 가능한 베이스 알고리즘의 종류는 다음과 같습니다. 사용할 수 있는 모델은 지속적으로 업데이트 중에 있습니다.
* Auto-ML 분류 모델 - [__AutomlClassifier__]()
* Auto-ML 회귀 모델 - [__AutomlRegressor__]()
* 추천 모델 - [__Light_FM__]()
* 추천 모델 - [__LDAREC__]()


## 2. FIT MODEL Clause
"__FIT MODEL__"은 이미 학습된 모델에 새로운 데이터를 추가하여 재학습 시킬때 사용되는 쿼리 구문 입니다. 최초 학습 이후 데이터가 추가적으로 확보 되어 기존 모델을 업데이트 하거나,  사전학습 된 인공지능 오픈소스 모델을 불러와 나만의 데이터로 재학습 시킬 경우 사용할 수 있습니다.
>>>>>>> 6d192ca8c0ec1de429143239776e34ae0e8bb09d

### 2.1 FIT MODEL Statement

```python
<<<<<<< HEAD
FIT MODEL [사용자 지정 모델이름]
USING [빌드했던 모델 이름]
OPTIONS (
         [모델 필요한 변수 옵션]
        ) 
AS [새로운 학습 데이터 세트]            
```


### 2.2 FIT MODEL 쿼리 구문 사용 예시
아래 예시는 FIT MODEL 쿼리 구문을 사용하여 이전에 만들었던 `test_classifier` 모델에 새로운 데이터 세트를 추가 학습하여 `fit_test_classifier`라는 모델을 만듭니다. 
=======
FIT MODEL [사용자 지정 모델 이름]
USING [빌드했던 모델 이름]
OPTIONS ([알고리즘 별 모델 빌드에 필요한 파라미터 등의 옵션값]) 
AS [사용할 데이터 세트]            
```


### 2.2 FIT MODEL Examples
아래 예는 "__FIT MODEL__" 쿼리 구문을 사용하여 이전에 만들었던 `test_classifier` 모델에 새로운 데이터 세트를 추가 학습하여 `fit_test_classifier`라는 모델을 만듭니다. 
>>>>>>> 6d192ca8c0ec1de429143239776e34ae0e8bb09d
>>>>>>> Stashed changes

```python
%%thanosql
FIT MODEL fit_test_classifier
USING test_classifier
OPTIONS (
 target='survived', 
 impute_type='iterative'
 ) 
AS 
SELECT * 
FROM titanic_train 
LIMIT 100
```

## 3. DELETE MODEL Clause

<<<<<<< Updated upstream

"__DELETE MODEL__" 구문은 ThanoSQL 데이터베이스에 만들어진 모델을 삭제합니다. 

### 3.1 DELETE MODEL Statement

=======
<<<<<<< HEAD
    

## 3. DELETE 쿼리 구문 설명

`DELETE MODEL` 구문은 Thanosql DB에 만들어진 모델을 삭제합니다. 

### 3.1 DELETE MODEL 구문의 기본 쿼리 구조
=======

"__DELETE MODEL__" 구문은 ThanoSQL 데이터베이스에 만들어진 모델을 삭제합니다. 

### 3.1 DELETE MODEL Statement
>>>>>>> 6d192ca8c0ec1de429143239776e34ae0e8bb09d

>>>>>>> Stashed changes
```python
DELETE MODEL [지우려는 모델 이름]
```

<<<<<<< Updated upstream
### 3.2 DELETE MODEL Examples
=======
<<<<<<< HEAD
### 3.2 DELETE MODEL 사용 예시

아래 예시는 1.2 단계에서 만들었던 `user_rec` 이라는 추천시스템을 thanosql DB 상에서 삭제합니다. 
=======
### 3.2 DELETE MODEL Examples
>>>>>>> 6d192ca8c0ec1de429143239776e34ae0e8bb09d
>>>>>>> Stashed changes

아래 예는 "__DELETE MODEL__" 쿼리 구문을 사용하여 [1.2.1 단계](#1_2_1)에서 만들었던 `user_rec` 라는 추천 모델을 ThanoSQL 데이터베이스에서 삭제합니다. 

```python
%%thanosql
DELETE MODEL user_rec
```

>### 쿼리 세부정보
<<<<<<< Updated upstream
>"__DELETE MODEL__" 쿼리 구문을 사용하여 `user_rec` 라는 모델을 ThanoSQL 데이터베이스에서 삭제합니다.

## 4. TRANSFORM USING Clause

### 4.1 TRANSFORM USING Statement

=======
<<<<<<< HEAD
>```DELETE MODEL``` 쿼리를 사용하여 user_rec 이라는 모델을 ThanoSQL DB에서 삭제합니다.

## 4. TRANSFORM 쿼리 구문 설명

### 4.1 TRANSFORM 쿼리구문 기본 구조
=======
>"__DELETE MODEL__" 쿼리 구문을 사용하여 `user_rec` 라는 모델을 ThanoSQL 데이터베이스에서 삭제합니다.

## 4. TRANSFORM USING Clause

### 4.1 TRANSFORM USING Statement
>>>>>>> 6d192ca8c0ec1de429143239776e34ae0e8bb09d

>>>>>>> Stashed changes
```python
TRANSFORM USING [사용할 변환 알고리즘]
AS [사용할 데이터 세트]
```

<<<<<<< Updated upstream
### 4.2 TRANSFORM USING Examples
=======
<<<<<<< HEAD
### 4.2 TRANSFORM 쿼리구문 사용예시

=======
### 4.2 TRANSFORM USING Examples
>>>>>>> 6d192ca8c0ec1de429143239776e34ae0e8bb09d
>>>>>>> Stashed changes

```python
%%thanosql
TRANSFORM USING test_classifier 
AS 
SELECT * 
FROM titanic_test 
LIMIT 100
```

<<<<<<< Updated upstream
## 5. PREDICT USING Clause

"__PREDICT USING__"  쿼리 구문은 [1.2.1 단계](#1_2_1)에서 빌드한 인공지능 모델을 사용하여 예측, 분류, 추천 등의 작업을 수행합니다.

### 5.1 PREDICT USING Statement
=======
<<<<<<< HEAD
## 5. PREDICT 쿼리 구문 설명
=======
## 5. PREDICT USING Clause
>>>>>>> 6d192ca8c0ec1de429143239776e34ae0e8bb09d

"__PREDICT USING__"  쿼리 구문은 [1.2.1 단계](#1_2_1)에서 빌드한 인공지능 모델을 사용하여 예측, 분류, 추천 등의 작업을 수행합니다.

<<<<<<< HEAD
### 5.1 PREDICT 쿼리 구문 기본 구조
=======
### 5.1 PREDICT USING Statement
>>>>>>> 6d192ca8c0ec1de429143239776e34ae0e8bb09d
>>>>>>> Stashed changes

```python
PREDICT USING [사용할 모델 이름]
OPTIONS ([사용할 모델에서 필요한 파라미터 등의 옵션값])
AS [사용할 데이터 세트]
```

<<<<<<< Updated upstream
### 5.2 PREDICT USING Examples
=======
<<<<<<< HEAD
### 5.2 PREDICT 쿼리 구문 사용 예시
=======
### 5.2 PREDICT USING Examples
>>>>>>> 6d192ca8c0ec1de429143239776e34ae0e8bb09d
>>>>>>> Stashed changes

아래 예시는 "__PREDICT USING__" 쿼리 구문을 사용하여 [1.2.1 단계](#1_2_1)에서 빌드한 `user_rec`라는 추천 모델을 사용하여 사용자 ID의 값이 31인 사용자가 좋아할만한 영화 목록 10개를 출력합니다.  


```python
%%thanosql 
PREDICT USING user_rec 
OPTIONS (
  predict_type='user', 
  user=31, 
  nrec=10
  )
AS 
SELECT * 
FROM news_train
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Avatar (2009)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Iron Man (2008)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Incredibles, The (2004)</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Pirates of the Caribbean: The Curse of the Bla...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Up (2009)</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Harry Potter and the Goblet of Fire (2005)</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Monsters, Inc. (2001)</td>
    </tr>
    <tr>
      <th>7</th>
      <td>WALL·E (2008)</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Harry Potter and the Deathly Hallows: Part 1 (...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Chronicles of Narnia: The Lion, the Witch and ...</td>
    </tr>
  </tbody>
</table>
</div>



<<<<<<< Updated upstream
>### 쿼리 세부정보 :
>"__PREDICT USING__" 쿼리구문을 사용하여 이전 단계에서 만든 `user_rec` 모델을 예측에 사용합니다. 추천모델에서는 예측 단계에서도 "__OPTIONS__"를 사용합니다. "predict_type"은 예측결과를 정렬할 대상 기준을 설정합니다. 특정 사용자(userid의 값이 31)에게 추천할 영화 목록을 보려고 하기 때문에 "user"를 적어줍니다. "user"는 보고자 하는 특정 사용자의 "userid"값인 31을 입력합니다. "nrec"는 추천하는 아이템의 개수를 의미합니다.

## 6. EVALUATE USING Clause

"__EVALUATE USING__" 쿼리 구문은 빌드한 모델의 성능을 평가합니다.

### 6.1 EVALUATE USING Statement

=======
<<<<<<< HEAD
>### 쿼리 세부정보
>```PREDICT USING``` 쿼리는 이전 단계에서 생성한 user_rec 이라는 모델을 사용하여 예측하게 합니다.
```OPTIONS(predict_type='predict_user', user_id=31, nrec=10, ...)``` 쿼리는 ```predict_type='predict_user', user_id=31, nrec=10``` 를 지정하여 유저ID 31번이 좋아할만한 아이템 10개를 예상하여 추천 목록을 출력합니다.

## 6. EVALUATE 쿼리 구문 설명

`EVALUATE` 쿼리 구문은 빌드한 모델의 성능을 평가합니다.

### 6.1 EVALUATE 쿼리구문 기본 구조
=======
>### 쿼리 세부정보 :
>"__PREDICT USING__" 쿼리구문을 사용하여 이전 단계에서 만든 `user_rec` 모델을 예측에 사용합니다. 추천모델에서는 예측 단계에서도 "__OPTIONS__"를 사용합니다. "predict_type"은 예측결과를 정렬할 대상 기준을 설정합니다. 특정 사용자(userid의 값이 31)에게 추천할 영화 목록을 보려고 하기 때문에 "user"를 적어줍니다. "user"는 보고자 하는 특정 사용자의 "userid"값인 31을 입력합니다. "nrec"는 추천하는 아이템의 개수를 의미합니다.

## 6. EVALUATE USING Clause

"__EVALUATE USING__" 쿼리 구문은 빌드한 모델의 성능을 평가합니다.

### 6.1 EVALUATE USING Statement
>>>>>>> 6d192ca8c0ec1de429143239776e34ae0e8bb09d

>>>>>>> Stashed changes
```python
EVALUATE USING [사용할 모델]
OPTIONS ([사용할 모델의 성능평가를 위한 변수값 설정])
AS [사용할 데이터 세트]
```

<<<<<<< Updated upstream
### 6.2 EVALUATE USING Examples
=======
<<<<<<< HEAD
### 6.2 EVALUATE 쿼리구문 사용예시
=======
### 6.2 EVALUATE USING Examples
>>>>>>> 6d192ca8c0ec1de429143239776e34ae0e8bb09d
>>>>>>> Stashed changes


```python
%%thanosql
EVALUATE USING test_classifier 
OPTIONS (target='survived') 
AS 
SELECT * 
FROM titanic_train 
LIMIT 100
```

<<<<<<< Updated upstream
## 7. CREATE TABLE Clause

"__CREATE TABLE__" 쿼리 구문은 비정형 데이터(이미지, 음성, 영상)를 다양한 임베딩 알고리즘을 사용하여 벡터 형식으로 변환한 테이블을 생성 합니다.
 
### 7.1 CREATE TABLE Statement

```python
=======
<<<<<<< HEAD
## 7. CREATE TABLE 쿼리 구문 설명

`CREATE TABLE` 쿼리구문은 비정형파일을 다양한 임베딩 알고리즘을 사용하여 벡터형식으로 변환 합니다.

### 7.1 CREATE TABLE 쿼리구문 기본 구조

```POSTGRESQL
=======
## 7. CREATE TABLE Clause

"__CREATE TABLE__" 쿼리 구문은 비정형 데이터(이미지, 음성, 영상)를 다양한 임베딩 알고리즘을 사용하여 벡터 형식으로 변환한 테이블을 생성 합니다.
 
### 7.1 CREATE TABLE Statement

```python
>>>>>>> 6d192ca8c0ec1de429143239776e34ae0e8bb09d
>>>>>>> Stashed changes
CREATE TABLE [사용자 지정 테이블 이름]
USING [사용할 변환 알고리즘]
AS [사용할 데이터 세트]
```

<<<<<<< Updated upstream
### 7.2 CREATE TABLE Examples
=======
<<<<<<< HEAD
### 7.2 CREATE TABLE 쿼리구문 사용예시



=======
### 7.2 CREATE TABLE Examples
>>>>>>> 6d192ca8c0ec1de429143239776e34ae0e8bb09d
>>>>>>> Stashed changes

```python
%%thanosql
CREATE TABLE color_descriptor_table_test 
USING Color_Descriptor 
OPTIONS (
 data_type='image',
 file_type=['.jpg']
 ) 
FROM '/data/thanosAlgo/image_search/junyoung_test/'
```

<<<<<<< Updated upstream
## 8. CONVERT Clause

"__CONVERT__" 쿼리 구문은 이미지, 비디오, 음성 등 비정형 데이터의 정보를 이용해서 다양한 임베딩 알고리즘을 통해 벡터형식으로 변환하고 대상 테이블에 정형 컬럼을 추가합니다.

### 8.1 CONVERT Statement

=======
<<<<<<< HEAD
## 8. CONVERT 쿼리 구문 설명

`CONVERT` 쿼리구문은 이미지, 비디오, 음성 등 비정형 파일을 다양한 임베딩 알고리즘을 통해 벡터형식으로 변환 합니다.

### 8.1 CONVERT 쿼리구문 기본구조
=======
## 8. CONVERT Clause

"__CONVERT__" 쿼리 구문은 이미지, 비디오, 음성 등 비정형 데이터의 정보를 이용해서 다양한 임베딩 알고리즘을 통해 벡터형식으로 변환하고 대상 테이블에 정형 컬럼을 추가합니다.

### 8.1 CONVERT Statement
>>>>>>> 6d192ca8c0ec1de429143239776e34ae0e8bb09d

>>>>>>> Stashed changes
```python
CONVERT [사용자 지정 테이블 이름]
USING [사용할 변환 알고리즘]
AS [사용할 데이터 세트]
```

<<<<<<< Updated upstream
### 8.2 CONVERT Examples
=======
<<<<<<< HEAD
### 8.2 CONVERT 쿼리구문 사용예시

=======
### 8.2 CONVERT Examples
>>>>>>> 6d192ca8c0ec1de429143239776e34ae0e8bb09d
>>>>>>> Stashed changes

```python
CONVERT USING Color_Descriptor 
AS 
SELECT * 
FROM color_descriptor_table_test
```

<<<<<<< Updated upstream
## 9. SEARCH Clause

"__SEARCH__" 쿼리 구문은 정형/비정형 데이터에서 내용/의미/유사도 등을 검색 합니다.

### 9.1 SEARCH Statement

=======
<<<<<<< HEAD
## 9. SEARCH 쿼리 구문 설명

`SEARCH` 쿼리구문은 이미지, 비디오, 음성 파일 내 찾고자 하는 특정 파일을 검색 합니다.
### 9.1 SEARCH 쿼리구문 기본 구조
=======
## 9. SEARCH Clause

"__SEARCH__" 쿼리 구문은 정형/비정형 데이터에서 내용/의미/유사도 등을 검색 합니다.

### 9.1 SEARCH Statement
>>>>>>> 6d192ca8c0ec1de429143239776e34ae0e8bb09d

>>>>>>> Stashed changes
```python
SEARCH [사용자 지정 테이블 이름]
USING [사용할 변환 알고리즘]
AS [사용할 데이터 세트]
```

<<<<<<< Updated upstream
### 9.2 SEARCH Examples
=======
<<<<<<< HEAD
### 9.2 SEARCH 쿼리구문 사용예시

=======
### 9.2 SEARCH Examples
>>>>>>> 6d192ca8c0ec1de429143239776e34ae0e8bb09d
>>>>>>> Stashed changes

```python
SEARCH IMAGE images='/data/thanosAlgo/image_search/junyoung_test/20150617_132435.jpg' 
USING Color_Descriptor 
AS 
SELECT * 
FROM color_descriptor_table_test
```

<<<<<<< Updated upstream
## 10. PRINT Clause

"__PRINT__" 쿼리 구문을 사용하여 이미지, 음성, 비디오 파일을 출력합니다.

### 10.1 PRINT Statement

=======
<<<<<<< HEAD
## 10. PRINT 쿼리 구문 설명

`PRINT` 쿼리구문을 사용하여 이미지, 음성, 비디오 파일을 출력합니다.

### 10.1 PRINT 쿼리구문 기본구조
=======
## 10. PRINT Clause

"__PRINT__" 쿼리 구문을 사용하여 이미지, 음성, 비디오 파일을 출력합니다.

### 10.1 PRINT Statement
>>>>>>> 6d192ca8c0ec1de429143239776e34ae0e8bb09d

>>>>>>> Stashed changes
```python
PRINT IMAGE|AUDIO|VIDEO
AS [출력할 데이터 세트]
```

<<<<<<< Updated upstream
### 10.2 PRINT Examples
=======
<<<<<<< HEAD
### 10.2 PRINT 쿼리구문 사용예시
=======
### 10.2 PRINT Examples
>>>>>>> 6d192ca8c0ec1de429143239776e34ae0e8bb09d
>>>>>>> Stashed changes

```python
%%thanosql
PRINT IMAGE
AS 
SELECT * 
FROM junyong_img 
```

```python
%%thanosql
PRINT AUDIO
AS 
SELECT * 
FROM junyong_aud
```

```python
%%thanosql
PRINT VIDEO
AS 
SELECT * 
FROM junyong_vid
```
