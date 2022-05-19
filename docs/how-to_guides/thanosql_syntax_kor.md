```python
%load_ext thanosql
%thanosql http://localhost:8005/api/v1/query
```

    API URL is changed to http://localhost:8005/api/v1/query


# **ThanoSQL 쿼리 구문 사용 튜토리얼**

 * ThanoSQL은 ANSI/ISO 표준 준수하며 85% 이상의 표준 SQL 쿼리문들과 호환합니다.
 * ThanoSQL만의 쿼리기능을 통해 이미지/동영상 내의 내용 검색이 가능합니다.
 * ThanoSQL만의 쿼리기능을 통해 이미지분류모델, 회귀예측, 회귀분류, 추천시스템 등의 인공지능 모델들을 SQL쿼리문으로 쉽고 간단하게 만들 수 있습니다.

## **1. BUILD MODEL 쿼리 구문 설명**

`BUILD MODEL` 쿼리구문은 사용자가 원하는 인공지능 모델을 구축하고 학습시킵니다. 사용자는 인공지능에 대한 깊은 지식 또는 코딩능력이 없어도 간단하게 `BUILD MODEL` 쿼리 구문만을 사용하여 인공지능 모델을 개발 할 수 있습니다.

### **1.1 BUILD MODEL 쿼리 구문 기본 구조**

```postgresql
BUILD MODEL [사용자 지정 모델 이름] 
USING [사용하고 싶은 모델]
OPTIONS ([모델별 빌드에 필요한 파라미터등의 옵션값])
AS [사용하고자 하는 데이터세트] 
```

### **1.2 BUILD MODEL 사용 예시 (추천 시스템 Light_FM모델 사용)**

아래 예시는 `BUILD MODEL` 쿼리문을 사용하여 `user_rec`이라는 `Light_FM` 추천 모델을 구축합니다.


```python
%%thanosql
BUILD MODEL user_rec
USING Light_FM
OPTIONS (   
            user_col='userid',
            item_col='movieid',
            rating_col='rating',
            item_names= 'title'
        )
AS SELECT * FROM movielens_train 
```

>### **쿼리 세부정보**
>```BUILD MODEL``` 쿼리를 사용하여 movie_rec 이라는 모델을 만들고 학습시킵니다.
>```OPTIONS(user_col='userid',item_col='movieid',rating_col='rating', item_names= 'title', ...)``` 쿼리는 모델 생성에 필수적으로 필요한 movielens 샘플데이터셋의 유저칼럼이름, 아이템칼럼이름, 평점칼럼 이름들을 할당하여 데이터셋 전처리 및 모델 빌드가 가능하도록 합니다. 모델의 파라미터 튜닝 옵션들 또한 사용이 가능합니다. Options에 따로 파라미터 할당을 하지 않으면 기본적인 파라미터 값으로 모델이 생성됩니다. 사용가능한 파라미터에 대한 설명은 https://making.lyst.com/lightfm/docs/lightfm.html 를 통해 확인할 수 있습니다.

## **2. PREDICT**

`PREDICT`  쿼리 구문은 1단계에서 빌드한 인공지능 모델을 사용하여 예측, 분류, 추천 등의 작업을 수행합니다.

### **2.1 PREDICT 쿼리 구문 기본 구조**

```postgresql
PREDICT 
USING [사용하고 싶은 모델]
OPTIONS ([PREDICT에 필요한 파라미터등의 옵션값])
AS [사용하고자 하는 데이터세트]
```

### **2.2 PREDICT 쿼리 구문 사용 예시**

아래 예시는 `PREDICT USING` 쿼리 구문을 사용하여 1단계에서 빌드한 `user_rec`이라는 추천 모델을 사용하여 31번 유저가 좋아할만한 영화 목록 10개를 출력합니다.  


```python
%%thanosql 
PREDICT 
USING user_rec 
OPTIONS (predict_type='predict_user',user_id=31, nrec=10)
AS select * from news_train
```




<div>
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



>### **쿼리 세부정보**
>```PREDICT USING``` 쿼리는 이전 단계에서 생성한 user_rec 이라는 모델을 사용하여 예측하게 합니다.
```OPTIONS(predict_type='predict_user', user_id=31, nrec=10, ...)``` 쿼리는 ```predict_type='predict_user', user_id=31, nrec=10``` 를 지정하여 유저ID 31번이 좋아할만한 아이템 10개를 예상하여 추천 목록을 출력합니다.

## **3. CREATE** 

`CREATE` 쿼리구문은 비정형파일을 다양한 임베딩 알고리즘을 사용하여 변환 시킵니다.

### **3.1 CREATE 쿼리구문 기본 구조**

```POSTGRESQL
CREATE TABLE [사용자 지정 테이블 이름]
USING [사용하고 싶은 변환 알고리즘]
AS [변환하고자 하는 데이터세트]
```

### **3.2 CREATE 쿼리구문 사용예시**

아래 예시는 ㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇ


```python
%%thanosql
CREATE TABLE color_descriptor_table_test 
USING Color_Descriptor 
OPTIONS(data_type='image',file_type=['.jpg']) 
FROM '/data/thanosAlgo/image_search/junyoung_test/'
```

## **4. CONVERT**

```POSTGRESQL
CONVERT [사용자 지정 테이블 이름]
USING [사용하고 싶은 변환 알고리즘]
AS [변환하고자 하는 데이터세트]
```


```python
CONVERT USING Color_Descriptor 
AS SELECT * FROM color_descriptor_table_test
```

## **5. DELETE**

`DELETE MODEL` 구문은 Thanosql DB에 만들어진 모델을 삭제합니다.

### **5.1 DELETE MODEL 구문의 기본 쿼리구조**

```POSTGRESQL
DELETE MODEL [지우고 싶은 모델명]
```

### **5.2 DELETE MODEL 사용 예시**

아래 예시는 1.2 단계에서 만들었던 `user_rec` 이라는 추천시스템을 thanosql DB 상에서 삭제합니다 


```python
%%thanosql
DELETE MODEL user_rec
```

>### **쿼리 세부정보**
>```DELETE MODEL``` 쿼리를 사용하여 user_rec 이라는 모델을 ThanoSQL DB에서 삭제합니다.

## **6. TRANSFORM**

```POSTGRESQL
TRANSFORM [사용자 지정 테이블 이름]
USING [사용하고 싶은 변환 알고리즘]
AS [변환하고자 하는 데이터세트]
```


```python
TRANSFORM USING test_classifier 
AS SELECT * FROM titanic_test LIMIT 100
```

## **7. SEARCH**


```python
SEARCH IMAGE images='/data/thanosAlgo/image_search/junyoung_test/20150617_132435.jpg' 
USING Color_Descriptor 
AS SELECT * FROM color_descriptor_table_test
```

## **8. PRINT**


```python
PRINT OOOO
USING
AS 
```
