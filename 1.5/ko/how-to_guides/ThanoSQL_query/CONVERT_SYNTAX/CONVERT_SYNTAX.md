---
title: CONVERT
---

# __CONVERT__

## __1. CONVERT 문__

사용자는 "__CONVERT__" 구문을 사용하여 이미지, 비디오, 음성, 텍스트 등 비정형 데이터를 수치화 알고리즘을 통해 벡터 형식으로 변환합니다.

## __2. CONVERT 구문__

```sql
query_statement:
    query_expr

CONVERT USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

## __3. CONVERT 예시__

!!! note
    - 예시는 한 모델에 특정된 것으로 필요한 옵션 값이나 사용되는 데이터 세트는 모델별로 다를 수 있습니다. 각 모델에 대한 자세한 설명은 [ThanoSQL Model Statement Reference](../../reference/#model-statement)를 참고 바랍니다.
    - 예시는 특정 모델과 데이터 세트가 존재해야만 작동하므로 그대로 복사하여 사용할 시 정상적으로 실행되지 않을 수 있습니다.

```sql
%%thanosql
CONVERT USING tutorial_search_clip
OPTIONS (
    image_col='image_path',
    convert_type='image',
    batch_size=128,
    result_col='convert_result'
    )
AS
SELECT *
FROM unsplash_data
```

!!! note "'__CONVERT__ 문'을 사용할 수 있는 인공지능 모델"
    - SimCLR 모델 - SimCLR
    - CLIP 모델 - CLIP
    - XCLIP 모델 - XCLIP
    - SBERT 모델 - SBERTKo, SBERTEn