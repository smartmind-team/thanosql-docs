## __6. LFM 모델__(OPTIONS.md)

### __BUILD MODEL 쿼리 구문__ 

이 "__BUILD MODEL__" 쿼리 구문을 사용하여 인공지능 모델을 개발할 수 있습니다. 
"__BUILD MODEL__" 표현식은 "__AS__" 뒤에 나오는 query_expr을 통해 정의된 데이터 세트를 학습할 수 있습니다. 

``` sql

BUILD MODEL [expression] 
USING Light_FM
OPTIONS (
    expression [ , ...]
    )
AS 
(query_expr)
``` 

__OPTIONS 절__

```sql
OPTIONS(
    (user_col = column_name),
    (item_col = column_name),
    (rating_col = column_name),
    [description_col = column_name],
    [norm = {True | False}],
    [threshhold =  (required if norm = True) VALUE]
    )
```

"__OPTIONS__" 절은 Light_FM 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다. 

- "user_col" : 데이터 테이블에서 사용자 ID 정보가 담겨있는 컬럼명을 설정합니다. (DEFAULT: "userid")
- "item_col" : 데이터 테이블에서 아이템 ID 정보가 담겨있는 컬럼명을 설정합니다. (DEFAULT: "itemid")
- "rating_col" : 데이터 테이블에서 평점 정보가 담겨있는 컬럼명을 설정합니다. (DEFAULT: "rating")
- "description_col" : 데이터 테이블에서 아이템 이름이 담겨있는 컬럼명을 설정합니다.(DEFAULT: None) 
- "norm" : 평점에 정규화가 필요하다면 True, 필요없다면 False로 설정합니다. (DEFAULT : False)
- "threshhold" : 평점이 정규화가 되었다면 임의의 임계값(0-1)을 설정합니다. 설정된 임계값 이상은 긍정으로 판단 (DEFAULT: None)
- "epoch" : 모델 학습 횟수를 설정합니다. (DEFAULT: 30)
- "n_jobs" : 학습때 사용할 코어수를 설정합니다 (DEFAULT: 4)  


__BUILD MODEL 쿼리 구문 예시__

[영화 평점 데이터를 사용하여 영화 추천 모델 만들기](/tutorials/thanosql_ml/recommendation/recommendation_lfm/)에서 해당 알고리즘 쿼리 구문 사용 예시를 확인하실 수 있습니다. 

```sql
%%thanosql
BUILD MODEL movie_rec
USING Light_FM
OPTIONS (
  user_col='userid',   
  item_col='movieid',
  rating_col='rating',
  description_col='title'
  )
AS 
SELECT * 
FROM movielens_train
```

### __PREDICT USING 쿼리 구문__

이 "__PREDICT USING__" 쿼리 구문을 사용하여 테스트 데이터 세트에 인공지능 모델을 적용하여 예측, 분류, 추천 등의 작업을 수행할 수 있습니다. "__PREDICT USING__" 표현식은 "__AS__" 뒤에 나오는 query_expr을 통해 정의한 데이터 세트를 전처리할 수 있습니다.


``` sql

PREDICT USING [expression] 
OPTIONS (
    expression [ , ...]
    )
AS 
(query_expr)
``` 

__OPTIONS 절__

```sql
OPTIONS(
    (predict_type = {'user' | 'item' | 'simitem'}),
    ({user_id | item_id}  = VALUE),
    [nrec = VALUE],
    [show= {True | False}],
    [threshold = VALUE] 
)
```

"__OPTIONS__" 절은 Light_FM  매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다. 

- "predict_type" : 평점 예측 종류를 설정합니다. 
> 'user': 특정 사용자 ID가 좋아할 아이템 목록을 예상하여 출력 합니다.  
> 'item': 아이템 ID를 기준으로 선택한 아이템을 좋아할만한 사용자 목록을 예상하여 출력합니다.  
> 'simitem': 특정 아이템 ID를 기준으로 유사한 아이템 목록을 예상하여 출력합니다.    
- "user_id | item_id" : "predict_type"에 따라 달라지는 변수입니다.  
> "user_id": "predict_type" 이 'user'일때 추천 대상 사용자 ID를 설정합니다.  
> "item_id":"predict_type" 이 'item' 또는 'simitem' 일때 좋아할 사용자 추천 또는 유사한 아이템 추천을 하고자하는 아이템 ID를 설정합니다.  
- "nrec" : 추천받을 아이템 갯수를 설정합니다. (DEFAULT : 10)  
- "show" : "predict_type" 이 'user' 일때 기존에 사용자가 소비한 아이템 목록을 함께 출력 되도록 설정합니다. (DEFAULT : False)   



__PREDICT USING 쿼리 구문 예시__

[영화 평점 데이터를 사용하여 영화 추천 모델 만들기](/tutorials/thanosql_ml/recommendation/recommendation_lfm/)에서 해당 알고리즘 쿼리 구문 사용 예시를 확인하실 수 있습니다. 

```sql
%%thanosql
PREDICT USING movie_rec
OPTIONS (
    predict_type='user', 
    user_id=31, 
    nrec=10
    )
AS 
SELECT * 
FROM movielens_train
```


### __3-1. 추천 모델 생성을 위한 "Light_FM" 인공지능 모델 사용__ (BUILD_MODEL_SYNTAX.md)

아래 예는 "__BUILD MODEL__" 구문을 사용하여 사용자가 정의한 <mark style="background-color:#E9D7FD ">my_movie_rec</mark> 모델을 ["__Light_FM__"](https://arxiv.org/pdf/1507.08439.pdf)이라는 인공지능 모델을 사용하여 추천 모델을 만듭니다. 전체 과정이 궁금하다면, [Movielens 영화 평점 데이터 세트를 사용하여 영화 추천 모델 만들기](/tutorials/thanosql_ml/recommendation/recommendation_lfm/)를 진행해 보세요.

```sql
%%thanosql
BUILD MODEL my_movie_rec
USING Light_FM
OPTIONS (
  user_col='userid',   
  item_col='movieid',
  rating_col='rating',
  description_col='title'
  )
AS 
SELECT * 
FROM movielens_data
```
!!! tip
    "__OPTIONS__"에 따로 옵션값을 할당하지 않으면 기본값으로 알고리즘 모델이 학습됩니다. 알고리즘 모델에서 사용 가능한 옵션들에 대한 추가적인 설명은 [참조 페이지](https://making.lyst.com/lightfm/docs/lightfm.html)를 통해 확인할 수 있습니다. 
