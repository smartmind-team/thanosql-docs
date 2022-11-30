---
title: SEARCH
---

# __SEARCH__

## __1. SEARCH 문__
"__SEARCH__" 쿼리 구문은 비정형 데이터에서 내용이나 의미 또는 유사도 등을 검색합니다.

## __2. SEARCH 구문__

```sql
%%thanosql
SEARCH IMAGE | AUDIO | VIDEO | TEXT | KEYWORD
USING [사용할 인공지능 모델]
OPTIONS ([모델 별 검색 시 필요한 옵션값])
AS [사용할 데이터 테이블]
```

!!! note "쿼리 세부 정보"
    - "OPTIONS" 쿼리 구문을 통해 사용할 옵션을 지정합니다.
        - "search_input_type": 검색할 때 사용할 이미지|텍스트|오디오|비디오 타입
        - "search_input": 검색할 때 사용할 입력값 
        - "emb_col": 데이터 테이블에서 수치화된 결과를 담은 컬럼
        - "column_name": 데이터 테이블에서 검색 결과를 담을 컬럼 이름 (default: "search_result")
## __3. SEARCH 예시__

!!! note 
    - 예시는 한 모델에 특정된 것으로 필요한 옵션 값이나 사용되는 데이터 세트는 모델별로 다를 수 있습니다. 각 모델에 대한 자세한 설명은 [ThanoSQL Pre-built Model Statement Reference](/ko/how-to_guides/reference/#thanosql-pre-built-model-statement-reference)를 참고해 주세요.
    - 예시는 특정 모델과 데이터 세트가 존재해야만 작동하므로 그대로 복사하여 사용할 시 정상적으로 실행되지 않을 수 있습니다.

```sql
%%thanosql
SEARCH IMAGE 
USING mymodel
OPTIONS (
    search_input_type="image",
    search_input="tutorial_data/mnist_data/test/923.jpg", 
    emb_col="convert_result",
    column_name="search_result"
) 
AS 
SELECT * 
FROM mnist_test
```