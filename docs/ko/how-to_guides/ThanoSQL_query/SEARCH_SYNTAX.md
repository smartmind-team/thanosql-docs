---
title: SEARCH
---

# __SEARCH__

## __1. SEARCH 문__

사용자는 "__SEARCH__" 구문을 사용하여 비정형 데이터 테이블에서 내용이나 의미 또는 유사도 등을 검색합니다.

## __2. SEARCH 구문__

```sql
query_statement:
    query_expr

SEARCH { IMAGE | AUDIO | VIDEO | TEXT | KEYWORD }
USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

!!! note "쿼리 세부 정보"
    - "OPTIONS" 절은 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.
        - "search_by": 검색할 때 사용할 이미지|텍스트|오디오|비디오 타입을 설정합니다. (str)
        - "search_input": 검색할 때 사용할 입력값입니다. (str)
        - "emb_col": 데이터 테이블에서 수치화된 결과를 담은 컬럼의 이름입니다. (str)
        - "result_col": 데이터 테이블에서 검색 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'search_result')

## __3. SEARCH 예시__

!!! note
    - 예시는 한 모델에 특정된 것으로 필요한 옵션 값이나 사용되는 데이터 세트는 모델별로 다를 수 있습니다. 각 모델에 대한 자세한 설명은 [ThanoSQL Model Statement Reference](/ko/how-to_guides/reference/#thanosql-model-statement-reference)를 참고해 주세요.
    - 예시는 특정 모델과 데이터 세트가 존재해야만 작동하므로 그대로 복사하여 사용할 시 정상적으로 실행되지 않을 수 있습니다.

```sql
%%thanosql
SEARCH IMAGE
USING my_image_search_model
OPTIONS (
    search_by='image',
    search_input='thanosql-dataset/mnist_data/test/923.jpg',
    emb_col='convert_result',
    result_col='search_result'
    )
AS
SELECT *
FROM mnist_test
```

!!! note "'__SEARCH__ 문'을 사용할 수 있는 인공지능 모델"
    - SimCLR 모델 - SimCLR
    - CLIP 모델 - CLIP
    - XCLIP 모델 - XCLIP
    - SBERT 모델 - SBERTKo, SBERTEn