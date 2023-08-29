---
title: GENERATE
---

# __GENERATE__

## __1. GENERATE 문__

사용자는 "__GENERATE__" 구문을 사용하여 생성형 모델을 적용하여 FAQ 생성, 키워드 추출, 서비스 평가, 감정 분석, 텍스트 요약, 등의 작업을 수행할 수 있습니다.

## __2. GENERATE 구문__

```sql
query_statement:
    query_expr

GENERATE USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

## __3. GENERATE 예시__

!!! note
    - 예시는 한 모델에 특정된 것으로 필요한 옵션 값이나 사용되는 데이터 세트는 모델별로 다를 수 있습니다.
    - 예시는 특정 모델과 데이터 세트가 존재해야만 작동하므로 그대로 복사하여 사용할 시 정상적으로 실행되지 않을 수 있습니다.

```sql
%%thanosql
GENERATE USING GPT
OPTIONS (
    task = 'faq', 
    text_col='text_data',
    result_col='faq_result'
    )
AS
SELECT *
FROM voc_text_data
```