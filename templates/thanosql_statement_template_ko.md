---
title: {구문 이름}
---

# __{구문 이름}__

## __1. {구문 이름} 문__

"__{구문 이름}__" {구문 설명}.

예시)
사용자는 데이터 과학에 대한 전문 지식이 없어도 "__BUILD MODEL__" 구문을 사용하여 원하는 인공지능 모델을 개발할 수 있습니다.

## __2. {구문 이름} 구문__

```sql
{구문 쿼리 설명
예시)
query_statement:
    query_expr

BUILD MODEL (model_name_expression)
USING {model_name_expression}
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
}
```

!!! note "쿼리 세부 정보"
    {쿼리 세부 정보 설명
    예시)
    - "__OPTIONS__" 절에서 매개변수의 값을 기본값으로부터 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.
        - "overwrite": 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (bool, optional, True|False, default: False)
    }

## __3. {구문 이름} 예시__

{필요하다면 부가 설명
예시)
!!! note
    - 예시는 한 모델에 특정된 것으로 필요한 옵션 값이나 사용되는 데이터 세트는 모델별로 다를 수 있습니다. 각 모델에 대한 자세한 설명은 [ThanoSQL Model Statement Reference](/ko/how-to_guides/reference/#thanosql-model-statement-reference)를 참고해 주세요.
    - 예시는 특정 모델과 데이터 세트가 존재해야만 작동하므로 그대로 복사하여 사용할 시 정상적으로 실행되지 않을 수 있습니다.
}

### __3-1. {예시 제목}__

```sql
%%thanosql
{구문 쿼리 예시
예시)
BUILD MODEL titanic_automl_classification
USING AutomlClassifier
OPTIONS (
    target_col='survived',
    impute_type='iterative',
    features_to_drop=['name', 'ticket', 'passengerid', 'cabin'],
    time_left_for_this_task=300,
    overwrite=True
    )
AS
SELECT *
FROM titanic_train
}
```

{필요하다면 부가 설명
예시)
!!! note "'__BUILD MODEL__ 문'을 사용할 수 있는 인공지능 모델"
    - AutoML 분류 모델 - AutomlClassifier
    - AutoML 회귀 모델 - AutomlRegressor
    - ConvNeXT 모델 - ConvNeXt_Tiny, ConvNeXt_Base
    - EfficientNet 모델 - EfficientNetV2S, EfficientV2M
    - Albert 모델 - AlbertKo, AlbertEn
    - Electra 모델 - ElectraKo, ElectraEn
    - Wav2Vec2 모델 - Wav2Vec2Ko, Wav2Vec2En
    - SimCLR 모델 - SimCLR
    - SBERT 모델 - SBERTKo, SBERTEn
}


<!-- 쿼리 세부 정보 예시 -->
<!-- BUILD, FIT -->
!!! note "쿼리 세부 정보"
    - "__OPTIONS__" 절에서 매개변수의 값을 기본값으로부터 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.
        - "overwrite": 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (bool, optional, True|False, default: False)

<!-- CONVERT, PREDICT -->
!!! note "쿼리 세부 정보"
    - "__OPTIONS__" 절에서 매개변수의 값을 기본값으로부터 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.
        - "table_name": ThanoSQL 워크스페이스 데이터베이스 내에 저장될 테이블 이름입니다. 기존에 사용한 테이블 이름으로 지정할 경우, 기존 테이블은 'convert_result' 컬럼을 추가한 테이블로 대체됩니다. 지정하지 않을 시 테이블을 저장하지 않습니다. (str, optional)

<!-- COPY -->
!!! note "쿼리 세부 정보"
    - "__OPTIONS__" 절에서 매개변수의 값을 기본값으로부터 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.
        - "overwrite": 동일 이름의 테이블이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 테이블은 새로운 테이블로 변경됩니다. (bool, optional, True|False, default: False)

<!-- GET -->
!!! note "쿼리 세부 정보"
    - "__OPTIONS__" 절에서 매개변수의 값을 기본값으로부터 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.
        - "model_name": 저장할 모델의 이름을 설정합니다. (str, optional)
        - "overwrite": 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (bool, optional, True|False, default: False)

!!! note "쿼리 세부 정보"
    - "__OPTIONS__" 절에서 매개변수의 값을 기본값으로부터 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.
        - "overwrite": 동일 이름의 데이터 세트가 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 데이터 세트는 새로운 데이터 세트로 변경됩니다. (bool, optional, True|False, default: False)

<!-- PRINT -->
!!! note "쿼리 세부 정보"
    - "__OPTIONS__" 절에서 매개변수의 값을 기본값으로부터 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.
        - "image_col | audio_col | video_col": 프린트 할 컬럼명을 지정합니다. (str, default: 'image_path'|'audio_path'|'video_path')

<!-- SEARCH -->
!!! note "쿼리 세부 정보"
    - "__OPTIONS__" 절에서 매개변수의 값을 기본값으로부터 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.
        - "search_by": 검색할 때 사용할 이미지|텍스트|오디오|비디오 타입을 설정합니다. (str)
        - "search_input": 검색할 때 사용할 입력값입니다. (str)
        - "emb_col": 데이터 테이블에서 수치화된 결과를 담은 컬럼의 이름입니다. (str)
        - "result_col": 데이터 테이블에서 검색 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'search_result')

<!-- UPLOAD MODEL -->
!!! note "쿼리 세부 정보"
    - "__OPTIONS__" 절에서 매개변수의 값을 기본값으로부터 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.
        - "framework": 모델의 프레임워크를 설정합니다. (str, default: 'pytorch')
        - "overwrite": 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (bool, optional, True|False, default: False)