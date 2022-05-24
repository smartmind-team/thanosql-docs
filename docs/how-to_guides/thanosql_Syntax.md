# ThanoSQL Syntax
## (안내 가이드) 쿼리 구문 사용 튜토리얼

**[이전 문서 - ](https://github.com/smartmind-team/thanosql-docs/blob/sunkeun/docs/tutorials/thanosql_ml/tabular/classification/automl_classification.md)** <br>| **[다음 문서 - ](https://github.com/smartmind-team/thanosql-docs/blob/indoo2/docs/tutorials/thanosql_ml/tabular/recommendation/lv1_lfm_kor_0_1.md)**

## 시작 전 사전정보

- 튜토리얼 난이도 : ★★☆☆☆
- 읽는데 걸리는 시간 : 5분
- 사용 언어 : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 마지막 수정날짜 : 2022-06-01

<br>

## 튜토리얼 소개
ThanoSQL에서 사용하는 문법들의 기능을 모아두었습니다. 각 문법의 사용 방법과 옵션을 제공합니다. 튜토리얼 문서와 함께 활용하면서 나만의 모델을 만들고 배포해 보세요. <br>
 * ThanoSQL은 [ANSI/ISO 표준](https://en.wikipedia.org/wiki/SQL_syntax)을 준수하며 85% 이상의 표준 SQL 쿼리 구문들과 호환합니다.
 * ThanoSQL만의 쿼리 기능을 통해 이미지/동영상 내의 내용 검색이 가능합니다.
 * ThanoSQL만의 쿼리 기능을 통해 이미지 분류 모델, 회귀 예측, 분류 예측, 추천 모델 등의 인공지능 알고리즘들을 SQL 쿼리 구문으로 쉽고 간단하게 만들 수 있습니다. <br><br>
 ![image](https://user-images.githubusercontent.com/50227222/169960904-1e884a17-39d8-404a-bbdb-a7c3450dfb3c.png)

 <br>
 
## ThanoSQL Clauses 미리보기

|Clause|Function|
|:---|:---|
|BUILD MODEL|모델 빌드|
|FIT MODEL|MODEL 재학습|
|DELETE MODEL|모델 삭제|
|TRANSFORM|테이블/데이터프레임 형태 변환|
|PREDICT|모델을 사용한 예측|
|EVALUATE|모델 성능 평가|
|CREATE TABLE|비정형데이터(이미지, 음성, 영상)의 임베딩 테이블 생성|
|CONVERT|비정형데이터(이미지, 음성, 영상) 임베딩 (벡터수치화)|
|SEARCH|비정형데이터(이미지, 음성, 영상) 내용 검색|
|PRINT|비졍형데이터(이미지, 음성, 영상) 출력|

<br>

## 1. BUILD MODEL Clause

"__BUILD MODEL__" 쿼리 구문은 사용자가 원하는 인공지능 모델을 구축하고 학습시킵니다. 사용자는 데이터 과학(Data Science)에 대한 전문지식이 없어도 간단하게 "__BUILD MODEL__" 쿼리 구문만을 사용하여 인공지능 모델을 개발 할 수 있습니다.

### 1.1 BUILD MODEL Statement

```python
BUILD MODEL [사용자 지정 모델 이름] 
USING [사용하고자 하는 베이스 알고리즘]
OPTIONS ([알고리즘 별 모델 빌드에 필요한 파라미터 등의 옵션값])
AS [사용하고자 하는 데이터세트] 
```

"__OPTIONS__"에서 사용하는 파라미터 등의 옵션값은 사용하는 베이스 알고리즘에 따라 다르게 적용됩니다. 각 알고리즘에 대한 "__OPTIONS__"는 [다음 문서]()에서 확인 가능합니다.

### 1.2 BUILD MODEL Examples

#### 1.2.1 추천 모델 구축을 위해 "__Light_FM__" 베이스 알고리즘 사용

아래 예시는 "__BUILD MODEL__" 쿼리문을 사용하여 `user_rec`라는 모델을 ["__Light_FM__"]()이라는 베이스 알고리즘을 학습시켜 추천 모델을 구축합니다. 자세한 코드 내용이 궁금하다면 [Movielens 영화평점 데이터를 이용한 영화 추천 모델 만들기](https://github.com/smartmind-team/thanosql-docs/blob/indoo2/docs/tutorials/thanosql_ml/tabular/recommendation/lv1_lfm_kor_0_1.md)를 진행해 보세요.

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
>"__OPTIONS__"에 따로 파라미터 할당을 하지 않으면 기본적인 파라미터 값으로 모델이 생성됩니다. <br> 베이스 알고리즘의 파라미터 튜닝 옵션들 또한 사용이 가능합니다. 사용가능한 파라미터에 대한 추가적인 설명은 [참조 페이지](https://making.lyst.com/lightfm/docs/lightfm.html)를 통해 확인할 수 있습니다.

#### 1.2.2 타이타닉 생존분류 데이터 세트 예시
아래 예시는 ["__AutomlClassifier__"]() 분류모델을 사용하여 타이타닉 생존여부 예측 모델을 만듭니다. 
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

## 2. FIT MODEL Clause
"__FIT MODEL__"은 이미 학습된 모델에 새로운 데이터를 추가하여 재학습 시킬때 사용되는 쿼리 구문 입니다. 최초 학습 이후 데이터가 추가적으로 확보 되어 기존 모델을 업데이트 하거나, 유명한 사전학습 된 인공지능 오픈소스 모델을 불러와 나만의 데이터로 재학습을 시킬 경우 사용 될 수 있습니다.

### 2.1 FIT MODEL Statement

```python
FIT MODEL [사용자 지정 모델이름]
USING [빌드했던 모델 이름]
OPTIONS ([알고리즘 별 모델 빌드에 필요한 파라미터 등의 옵션값]) 
AS [새로운 학습 데이터 세트]            
```


### 2.2 FIT MODEL Examples
아래 예는 "__FIT MODEL__" 쿼리 구문을 사용하여 이전에 만들었던 `test_classifier` 모델에 새로운 데이터 세트를 추가 학습하여 `fit_test_classifier`라는 모델을 만듭니다. 

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

## 3. DELETE 쿼리 구문 설명

`DELETE MODEL` 구문은 Thanosql 데이터베이스에 만들어진 모델을 삭제합니다. 

### 3.1 DELETE MODEL 구문의 기본 쿼리 구조

```POSTGRESQL
DELETE MODEL [지우고 싶은 모델명]
```

### 3.2 DELETE MODEL 사용 예시

아래 예시는 1.2 단계에서 만들었던 `user_rec` 이라는 추천시스템을 thanosql DB 상에서 삭제합니다. 


```python
%%thanosql
DELETE MODEL user_rec
```

>### 쿼리 세부정보
>```DELETE MODEL``` 쿼리를 사용하여 user_rec 이라는 모델을 ThanoSQL DB에서 삭제합니다.

## 4. TRANSFORM 쿼리 구문 설명

### 4.1 TRANSFORM 쿼리구문 기본 구조

```POSTGRESQL
TRANSFORM [사용자 지정 테이블 이름]
USING [사용하고 싶은 변환 알고리즘]
AS [변환하고자 하는 데이터세트]
```

### 4.2 TRANSFORM 쿼리구문 사용예시


```python
%%thanosql
TRANSFORM USING test_classifier 
AS SELECT * FROM titanic_test LIMIT 100
```

## 5. PREDICT 쿼리 구문 설명

`PREDICT`  쿼리 구문은 1단계에서 빌드한 인공지능 모델을 사용하여 예측, 분류, 추천 등의 작업을 수행합니다.

### 5.1 PREDICT 쿼리 구문 기본 구조

```postgresql
PREDICT 
USING [사용하고 싶은 모델]
OPTIONS ([PREDICT에 필요한 파라미터등의 옵션값])
AS [사용하고자 하는 데이터세트]
```

### 5.2 PREDICT 쿼리 구문 사용 예시

아래 예시는 `PREDICT USING` 쿼리 구문을 사용하여 1단계에서 빌드한 `user_rec`이라는 추천 모델을 사용하여 31번 유저가 좋아할만한 영화 목록 10개를 출력합니다.  


```python
%%thanosql 
PREDICT 
USING user_rec 
OPTIONS (predict_type='predict_user',user_id=31, nrec=10)
AS select * from news_train
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



>### 쿼리 세부정보
>```PREDICT USING``` 쿼리는 이전 단계에서 생성한 user_rec 이라는 모델을 사용하여 예측하게 합니다.
```OPTIONS(predict_type='predict_user', user_id=31, nrec=10, ...)``` 쿼리는 ```predict_type='predict_user', user_id=31, nrec=10``` 를 지정하여 유저ID 31번이 좋아할만한 아이템 10개를 예상하여 추천 목록을 출력합니다.

## 6. EVALUATE 쿼리 구문 설명

`EVALUATE` 쿼리 구문은 빌드한 모델의 성능을 평가합니다.

### 6.1 EVALUATE 쿼리구문 기본 구조

```postgresql
EVALUATE 
USING [사용하고 싶은 모델]
OPTIONS ([EVALUATE 변수값 설정])
AS [사용하고자 하는 데이터세트]
```

### 6.2 EVALUATE 쿼리구문 사용예시


```python
%%thanosql
EVALUATE USING test_classifier 
OPTIONS (
         target='survived'
        ) 
AS SELECT * FROM titanic_train LIMIT 100
```

## 7. CREATE TABLE 쿼리 구문 설명

`CREATE TABLE` 쿼리구문은 비정형파일을 다양한 임베딩 알고리즘을 사용하여 벡터형식으로 변환 합니다.

### 7.1 CREATE TABLE 쿼리구문 기본 구조

```POSTGRESQL
CREATE TABLE [사용자 지정 테이블 이름]
USING [사용하고 싶은 변환 알고리즘]
AS [변환하고자 하는 데이터세트]
```

### 7.2 CREATE TABLE 쿼리구문 사용예시




```python
%%thanosql
CREATE TABLE color_descriptor_table_test 
USING Color_Descriptor 
OPTIONS(data_type='image',file_type=['.jpg']) 
FROM '/data/thanosAlgo/image_search/junyoung_test/'
```

## 8. CONVERT 쿼리 구문 설명

`CONVERT` 쿼리구문은 이미지, 비디오, 음성 등 비정형 파일을 다양한 임베딩 알고리즘을 통해 벡터형식으로 변환 합니다.

### 8.1 CONVERT 쿼리구문 기본구조

```POSTGRESQL
CONVERT [사용자 지정 테이블 이름]
USING [사용하고 싶은 변환 알고리즘]
AS [변환하고자 하는 데이터세트]
```

### 8.2 CONVERT 쿼리구문 사용예시


```python
CONVERT USING Color_Descriptor 
AS SELECT * FROM color_descriptor_table_test
```

## 9. SEARCH 쿼리 구문 설명

`SEARCH` 쿼리구문은 이미지, 비디오, 음성 파일 내 찾고자 하는 특정 파일을 검색 합니다.
### 9.1 SEARCH 쿼리구문 기본 구조

```POSTGRESQL
SEARCH [사용자 지정 테이블 이름]
USING [사용하고 싶은 변환 알고리즘]
AS [변환하고자 하는 데이터세트]
```

### 9.2 SEARCH 쿼리구문 사용예시


```python
SEARCH IMAGE images='/data/thanosAlgo/image_search/junyoung_test/20150617_132435.jpg' 
USING Color_Descriptor 
AS SELECT * FROM color_descriptor_table_test
```

## 10. PRINT 쿼리 구문 설명

`PRINT` 쿼리구문을 사용하여 이미지, 음성, 비디오 파일을 출력합니다.

### 10.1 PRINT 쿼리구문 기본구조

```POSTGRESQL
PRINT IMAGE | AUDIO | VIDEO
AS [출력하고자 하는 파일]
```

### 10.2 PRINT 쿼리구문 사용예시

```python
%%thanosql
PRINT IMAGE
AS SELECT * FROM junyong_img 
```

```python
%%thanosql
PRINT AUDIO
AS SELECT * FROM junyong_aud
```


```python
%%thanosql
PRINT VIDEO
AS SELECT * FROM junyong_vid
```
