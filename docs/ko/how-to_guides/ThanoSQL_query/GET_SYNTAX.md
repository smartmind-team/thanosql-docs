---
title: GET
---

# __GET__

## __1. GET 문__

사용자는 "__GET__" 구문을 사용하여 현재 ThanoSQL의 Pre-built 모델들("THANOSQL MODEL")과 튜토리얼 데이트 세트들을 받아올 수 있습니다. 

## __2. GET 구문__

"__GET THANOSQL MODEL__" 구문은 ThanoSQL의 Pre-built 모델들을 자신의 워크스페이스로 가지고 올 수 있습니다.

```sql
%%thanosql
GET THANOSQL MODEL [ThanoSQL Pre-built 모델 이름] 
OPTIONS (
    overwrite=True
) 
AS 
[사용자 지정 모델 이름]
```

!!! note "__Note__"    
    - LIST THANOSQL MODEL 구문을 사용하여 ThanoSQ의 Pre-built 모델들의 리스트를 확인할 수 있습니다.  
    - GET 구문 사용 시 AS 와 사용자 지정 모델 이름을 표기하지 않는다면 ThanoSQL Pre-built 모델의 이름으로 저장됩니다.

!!! note "쿼리 세부 정보"
    - "OPTIONS" 쿼리 구문을 통해 사용할 옵션을 지정합니다.
        - "overwrite": 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (True|False, default : False)

"__GET THANOSQL DATASET__" 구문은 ThanoSQL Tutorial에 사용되는 테이터 세트를 자신의 워크스페이스 Dataset 폴더로 가지고 올 수 있습니다. 

```sql
%%thanosql
GET THANOSQL DATASET [ThanoSQL 데이터 세트 이름]
OPTIONS (
    overwrite=True 
)
```

!!! note "__Note__"    
    - LIST THANOSQL DATASET 구문을 사용하여 ThanoSQ의 데이터 세트들의 리스트를 확인할 수 있습니다.  
    - GET THANOSQL DATASET 구문은 지정된 데이터 세트의 이름을 바꿀 수 없습니다. 

!!! note "쿼리 세부 정보"
    - "OPTIONS" 쿼리 구문을 통해 사용할 옵션을 지정합니다.
        - "overwrite":동일 이름의 데이터 세트가 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 데이터 세트은 새로운 데이터 세트로 변경됩니다. (True|False, default : False) 

## __3. GET 예시__ 

### __3.1 GET THANOSQL MODEL 예시__

```sql
%%thanosql
GET THANOSQL MODEL convnext
OPTIONS (
    overwrite=True
)
AS myconvnext
```

### __3.2 GET THANOSQL DATASET 예시__

```sql
%%thanosql
GET THANOSQL DATASET unsplash_data
OPTIONS (
    overwrite=True
)
```

