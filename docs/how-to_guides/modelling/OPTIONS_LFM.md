# __ThanoSQL 알고리즘 쿼리 구문__

쿼리 구문은 표현식을 한 개 이상 검색하고 계산된 결과 테이블을 반환합니다. 이 페이지에서는 ThanoSQL에서 사용하는 알고리즘 쿼리에 관한 구문을 설명합니다.

**[이전 문서 - 결과 출력하기](/how-to_guides/modelling/PRINT_SYNTAX/)**  

__표기법 규칙__ 

- 대괄호 `[]`는 선택적 절을 나타냅니다.   
- 괄호 `()`는 ^^리터럴^^ 괄호를 나타냅니다.  
- 세로 막대 `|`는 논리 `OR`를 나타냅니다.  
- 중괄호 { }는 옵션 조합을 묶는 데 사용됩니다.  
- 대괄호 [ , ... ] 안에 있는 쉼표 다음에 오는 줄임표는 앞의 항목이 쉼표로 구분된 
목록으로 반복될 수 있음을 의미합니다.
- VALUE는 숫자값을 의미합니다. 

!!! note "" 
    __리터럴__ : 고정되거나 변경할 수 없는 값을 의미하며 상수(constant)라고도 불립니다. 
    > 각 리터럴은 테이블에서 열과 같은 특별한 자료형을 가지고 있다.

## __1. LFM추천 모델__

### __BUILD MODEL 쿼리 구문__ 

이 "__BUILD MODEL__" 쿼리 구문을 사용하여 인공지능 모델을 개발할 수 있습니다. 
"__BUILD MODEL__" 표현식은 AS 뒤에 나오는 query_expr을 통해 정의된 데이터 세트를 학습할 수 있습니다. 

``` sql
query_statement:
    query_expr

BUILD MODEL [expression] 
USING AutomlClassifier
OPTIONS (
    expression [ , ...]
)
AS 
(query_expr)
``` 

#### __OPTIONS 절__

```sql
OPTIONS(
    (user_col = column_name),
    (item_col = column_name),
    (rating_col = column_name),
    [description = column_name],
    [norm = {True | False}],
    [threshhold =  (required if norm = True) VALUE]
)
```

"__OPTIONS__" 절은 AutomlClassifier의 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다. 

- "user_col" : 데이터 테이블에서 유저 ID 정보가 담겨있는 칼럼명을 설정합니다. (DEFAULT: "userid")
- "item_col" : 데이터 테이블에서 아이템 ID 정보가 담겨있는 칼럼명을 설정합니다. (DEFAULT: "itemid")
- "rating_col" : 데이터 테이블에서 평점 정보가 담겨있는 칼럼명을 설정합니다. (DEFAULT: "rating")
- "description" : 데이터 테이블에서 아이템 이름이 담겨있는 칼럼명을 설정합니다.(DEFAULT: None) 
- "norm" : 평점에 정규화가 필요하다면 True, 필요없다면 False로 설정합니다. (DEFAULT : False)
- "threshhold" : 평점이 정규화가 되었다면 임의의 임계값(0-1)을 설정합니다. 설정된 임계값 이상은 긍정으로 판단 (DEFAULT: None)
- "epoch" : 모델 학습 횟수를 설정합니다. (DEFAULT: 30)
- "n_jobs" : 학습때 사용할 코어수를 설정합니다 (DEFAULT: 4)  


#### __BUILD MODEL 쿼리 구문 예시__

[Movielens 영화 평점 데이터 세트를 사용하여 영화 추천 모델 만들기](/tutorials/thanosql_ml/recommendation/recommendation_lfm/)에서 해당 알고리즘 쿼리 구문 사용 예시를 확인하실 수 있습니다. 

```sql
%%thanosql
BUILD MODEL movie_rec
USING Light_FM
OPTIONS (
  user_col='userid',   
  item_col='movieid',
  rating_col='rating',
  description='title'
  )
AS 
SELECT * 
FROM movielens_train
```

### __PREDICT USING 쿼리 구문__

이 "__PREDICT USING__" 쿼리 구문을 사용하여 테스트 데이터 세트에 인공지능 모델을 적용하여 예측, 분류, 추천 등의 작업을 수행할 수 있습니다."__PREDICT USING__" 표현식은 AS 뒤에 나오는 query_expr을 통해 정의한 데이터 세트를 전처리할 수 있습니다.


``` sql
query_statement:
    query_expr

PREDICT USING [expression] 
OPTIONS (
    expression [ , ...]
)
AS 
(query_expr)
``` 

#### __OPTIONS 절__

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
    >'user': 특정 유저ID가 좋아할 아이템 목록을 예상하여 출력 합니다.
    >'item': 아이템 ID를 기준으로 선택한 아이템을 좋아할만한 유저 목록을 예상하여 출력합니다.
    >'simitem': 특정 아이템 ID를 기준으로 유사한 아이템 목록을 예상하여 출력합니다.  
- "user_id | item_id" : "predict_type"에 따라 달라지는 변수입니다.
    >"user_id": "predict_type" 이 'user'일때 추천 대상 유저 ID를 설정합니다.
    >"item_id":"predict_type" 이 'item' 또는 'simitem' 일때 좋아할 유저추천 또는 유사한 아이템 추천을 하고자하는 아이템 ID를 설정합니다.
- "nrec" : 추천받을 아이템 갯수를 설정합니다. (DEFAULT: 10)
- "show" : "predict_type" 이 'user' 일때 기존에 유저가 소비한 아이템 목록을 함께 출력 되도록 설정합니다.(DEFAULT: False) 



#### __PREDICT USING 쿼리 구문 예시__
[Movielens 영화 평점 데이터 세트를 사용하여 영화 추천 모델 만들기](/tutorials/thanosql_ml/recommendation/recommendation_lfm/)에서 해당 알고리즘 쿼리 구문 사용 예시를 확인하실 수 있습니다. 

```sql
%%thanosql
PREDICT USING movie_rec
OPTIONS (
  predict_type='user', 
  user_col=31, 
  nrec=10
  )
AS 
SELECT * 
FROM movielens_train
```


