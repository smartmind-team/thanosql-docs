---
title: SEARCH
---

# __SEARCH__

## __1. SEARCH 문__
"__SEARCH__" 쿼리 구문은 비정형 데이터에서 내용이나 의미 또는 유사도 등을 검색합니다.

## __2. SEARCH 구문__

```sql
%%thanosql
SEARCH IMAGE | AUDIO | VIDEO [검색에 사용되는 입력 데이터]
USING [사용할 인공지능 모델]
OPTIONS ([모델 별 검색 시 필요한 옵션값])
AS [사용할 데이터 테이블]
```

!!! note "쿼리 세부 정보"
    - "OPTIONS" 쿼리 구문을 통해 사용할 옵션을 지정합니다.
        - "table_name": 구문을 통해 나온 결과 값을 저장할 테이블명을 지정합니다. 테이블명을 지정하지 않으면 결과 값은 따로 저장되지 않습니다.

## __3. SEARCH 예시__

!!! note 
    - 예시는 한 모델에 특정된 것으로 필요한 옵션 값이나 사용되는 데이터 세트는 모델별로 다를 수 있습니다. 각 모델에 대한 자세한 설명은 [ThanoSQL Pre-built Model Statement Reference](/ko/how-to_guides/reference/#thanosql-pre-built-model-statement-reference)를 참고해 주세요.
    - 예시는 특정 모델과 데이터 세트가 존재해야만 작동하므로 그대로 복사하여 사용할 시 정상적으로 실행되지 않을 수 있습니다.

```sql
%%thanosql
SEARCH IMAGE images='tutorial_data/mnist_data/test/923.jpg' 
USING mymodel
OPTIONS (
    table_name='search_result_table'
) 
AS 
SELECT * 
FROM mnist_test
```