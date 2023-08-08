---
title: PREDICT
---

# __PREDICT__

## __1. PREDICT 문__

사용자는 "__PREDICT__" 구문을 사용하여 인공지능 모델을 적용하여 예측, 분류, 추천 등의 작업을 수행할 수 있습니다.

## __2. PREDICT 구문__

```sql
query_statement:
    query_expr

PREDICT USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

## __3. PREDICT 예시__

!!! note
    - 예시는 한 모델에 특정된 것으로 필요한 옵션 값이나 사용되는 데이터 세트는 모델별로 다를 수 있습니다. 각 모델에 대한 자세한 설명은 [ThanoSQL Model Statement Reference](../../reference/#model-statement)를 참고 바랍니다.
    - 예시는 특정 모델과 데이터 세트가 존재해야만 작동하므로 그대로 복사하여 사용할 시 정상적으로 실행되지 않을 수 있습니다.

```sql
%%thanosql
PREDICT USING my_product_classifier
OPTIONS (
    image_col='image_path',
    result_col='predict_result'
    )
AS
SELECT *
FROM product_image_test
```

!!! note "'__PREDICT__ 문'을 사용할 수 있는 인공지능 모델"
    - AutoML 분류 모델 - AutomlClassifier
    - AutoML 회귀 모델 - AutomlRegressor
    - ConvNeXT 모델 - ConvNeXt_Tiny, ConvNeXt_Base
    - EfficientNet 모델 - EfficientNetV2S, EfficientV2M
    - Albert 모델 - AlbertKo, AlbertEn
    - Electra 모델 - ElectraKo, ElectraEn
    - Wav2Vec2 모델 - Wav2Vec2Ko, Wav2Vec2En
    - Whisper 모델 - Whisper
    - ViLT 모델 - ViLT
    - TFT 모델 - TFT