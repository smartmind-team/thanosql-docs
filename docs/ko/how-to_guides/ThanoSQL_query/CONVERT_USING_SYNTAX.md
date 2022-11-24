---
title: CONVERT
---

# __CONVERT__

## __1. CONVERT 문__

사용자는 "__CONVERT__"  구문은 이미지, 비디오, 음성 등 비정형 데이터의 정보를 이용해서 수치화 알고리즘을 사용하여 벡터 형식으로 변환하고 이 값을 사용할 데이터 세트에 추가합니다.

## __2. CONVERT 구문__

```sql
CONVERT USING [사용할 인공지능 모델]
OPTIONS [모델 별 수치화 시 필요한 옵션값]
AS 
[사용할 데이터 세트]
```

!!! note "쿼리 세부 정보"
    - "OPTIONS" 쿼리 구문을 통해 사용할 옵션을 지정합니다.
        - "table_name":구문을 통해 나온 결과 값을 저장할 테이블명을 지정합니다. 테이블명을 지정하지 않으면 결과 값은 따로 저장되지 않습니다.

## __3. CONVERT 예시__ 

!!! note 
    - 예시는 한 모델에 특정된 것으로 필요한 옵션 값이나 사용되는 데이터 세트는 모델별로 다를 수 있습니다. 각 모델에 대한 자세한 설명은 [ThanoSQL Pre-built Model Statement Reference](/how-to_guides/reference/#thanosql-pre-built-model-statement-reference)를 참고해 주세요.
    - 예시는 특정 모델과 데이터 세트가 존재해야만 작동하므로 그대로 복사하여 사용할 시 정상적으로 실행되지 않을 수 있습니다.

### __3.1 `CLIP` 모델을 사용한 이미지 수치화__ 

```sql
%%thanosql
CONVERT USING clip
OPTIONS (
    image_col="image_path",
    table_name="unsplash_data", 
    batch_size=128
    )
AS 
SELECT * 
FROM unsplash_data
```

### __3.2 `SimCLR` 모델을 사용한 이미지 수치화__

```sql
%%thanosql
CONVERT USING simclr
OPTIONS (
    table_name= "convert_result_table",
    image_col="image_path"
    )
AS 
SELECT * 
FROM mnist_test
```