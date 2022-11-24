---
title: BUILD MODEL
---

# __BUILD MODEL__ 

## __1. BUILD MODEL 문__

사용자는 데이터 과학(Data Science)에 대한 전문 지식이 없어도 간단하게 "__BUILD MODEL__" 구문을 사용하여 원하는 인공지능 모델을 개발할 수 있습니다.

## __2. BUILD MODEL 구문__

```sql
%%thanosql
BUILD MODEL [사용자 지정 모델 이름]
USING [사용할 인공지능 모델]
OPTIONS ([인공지능 모델을 만들 때 필요한 옵션값])
AS 
[사용할 데이터 세트]
```

!!! note "쿼리 세부 정보"
    - "OPTIONS" 쿼리 구문을 통해 사용할 옵션을 지정합니다.
        - "overwrite":동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (True|False, DEFAULT : False)

## __3. BUILD MODEL 예시__

!!! note 
    - 예시는 한 모델에 특정된 것으로 필요한 옵션 값이나 사용되는 데이터 세트는 모델별로 다를 수 있습니다. 각 모델에 대한 자세한 설명은 [ThanoSQL Pre-built Model Statement Reference](/how-to_guides/reference/#thanosql-pre-built-model-statement-reference)를 참고해 주세요.
    - 예시는 특정 모델과 데이터 세트가 존재해야만 작동하므로 그대로 복사하여 사용할 시 정상적으로 실행되지 않을 수 있습니다.

### __3-1. AutomlClassifier 모델을 사용한 분류 모델 생성__

```sql
%%thanosql
BUILD MODEL mymodel 
USING AutomlClassifier 
OPTIONS (
    target='survived', 
    impute_type='iterative',  
    features_to_drop=["name", 'ticket', 'passengerid', 'cabin'],
    overwrite = True
    )
AS
SELECT *
FROM titanic_train
```

!!! note "'__BUILD MODEL__'을 통해 사용 가능한 인공지능 모델"
    - Auto-ML 분류 모델 - AutomlClassifier
    - Auto-ML 회귀 모델 - AutomlRegressor
    - ConvNeXT 모델 - ConvNeXt_Tiny, ConvNeXt_Base
    - EfficientNet 모델 - EfficientNetV2S, EfficientV2M
    - Albert 모델 - AlbertKo, AlbertEn 
    - Electra 모델 - ElectraKo, ElectraEn
    - Wav2Vec2 모델 - Wav2Vec2Ko, Wav2Vec2En
    - SBERT 모델 - SBERTKo, SBERTEn