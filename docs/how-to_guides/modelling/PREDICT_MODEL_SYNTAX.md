# __모델 적용하기 (PREDICT USING)__

**[이전 문서 - 모델 적용을 위해 데이터 전처리하기](/how-to_guides/modelling/TRANSFORM_MODEL_SYNTAX/)**  
**[다음 문서 - 모델 삭제하기](/how-to_guides/modelling/DELETE_MODEL_SYNTAX/)**


## __1. PREDICT USING 구문 개요__

사용자는 "__PREDICT USING__" 구문을 사용하여 테스트 데이터 세트에 인공지능 모델을 적용하여 예측, 분류, 추천 등의 작업을 수행할 수 있습니다.  

## __2. PREDICT USING 구문__ 

```sql
%%thanosql
PREDICT USING [기존 학습한 모델 이름]
OPTIONS ([, 모델 별 추론시 필요한 옵션값])
AS
[사용할 테스트 데이터 세트]
```

## __3. PREDICT USING 구문 예시__ 
아래 예는 "__PREDICT USING__" 구문을 사용하여 사용자가 [모델 학습하기](/how-to_guides/modelling/BUILD_MODEL_SYNTAX/)에서 만들었던 <mark style="background-color:#E9D7FD ">user_rec</mark> 추천 모델을 사용하여 사용자 ID의 값이 31인 사용자가 좋아할 만한 영화 목록을 추천합니다.

```sql
%%thanosql
PREDICT USING user_rec
OPTIONS (
  predict_type='user', 
  user=31, 
  nrec=10
  )
AS 
SELECT * 
FROM movielens_train
```

!!! note "__쿼리 세부 정보__" 
    __"PREDICT USING"__ 구문을 사용하여 [모델 학습하기](/how-to_guides/modelling/BUILD_MODEL_SYNTAX/)에서 만든 <mark style="background-color:#E9D7FD ">user_rec</mark> 모델을 예측에 사용합니다. 
    추천 모델에서는 예측 단계에서도 "__OPTIONS__"를 사용합니다. "predict_type"은 예측 결과를 정렬할 대상 기준을 설정합니다.  
    예시에서는 특정 사용자(<mark style="background-color:#D7D0FF ">userid</mark>의 값이 31)에게 추천할 영화 목록을 보려고 하기 때문에 "user"를 적어주며 "user"는 보고자 하는 특정 사용자의 <mark style="background-color:#D7D0FF ">userid</mark>값인 31을 입력합니다. "nrec"는 추천하는 아이템의 개수를 의미합니다. 


