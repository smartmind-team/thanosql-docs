---
title: Temporal Fusion Transformer(TFT)
---

# ____

__표기법 규칙__

- 괄호 `()`는 ^^리터럴^^ 괄호를 나타냅니다.
- 중괄호 {}는 옵션 조합을 묶는 데 사용됩니다.
- 대괄호 `[]`는 선택적 절을 나타냅니다.
- 대괄호 [ , ... ] 안에 있는 쉼표 다음에 오는 줄임표는 앞의 항목이 쉼표로 구분된 
목록으로 반복될 수 있음을 의미합니다.
- 세로 막대 `|`는 논리 `OR`를 나타냅니다.
- VALUE는 값을 의미합니다.

!!! note ""
    - __리터럴__: 고정되거나 변경할 수 없는 값을 의미하며 상수(Constant)라고도 불립니다.
    > 각 리터럴은 테이블에서 컬럼과 같은 특별한 자료형을 가지고 있습니다.

## __BUILD MODEL 구문__

"__BUILD MODEL__" 구문을 사용하여 인공지능 모델을 개발할 수 있습니다. "__BUILD MODEL__" 구문은 "__AS__" 뒤에 나오는 query_expr을 통해 정의된 데이터 세트를 사용하여 모델을 학습할 수 있습니다.


```sql
query_statement:
    query_expr

BUILD MODEL (model_name_expression)
USING TFT
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

__OPTIONS 절__

```sql
OPTIONS(
    (max_encoder_length=VALUE),
    [min_encoder_length=VALUE],
    (max_prediction_length=VALUE),
    [min_prediction_length=VALUE],
    (time_idx_col=VALUE),
    (target_col=VALUE),
    (group_id_cols=VALUE),
    [group_normalizer={True|False}]
    (static_categorical_cols=VALUE),
    (static_real_cols=VALUE),
    (time_varying_known_categorical_cols=VALUE),
    (time_varying_known_real_cols=VALUE),
    (time_varying_unknown_categorical_cols=VALUE),
    (time_varying_unknown_real_cols: VALUE),
    (special_day_cols=VALUE), 
    (allow_missing_timesteps={True|False}),
    [validate={True|False}],
    [batch_size=VALUE],
    [max_epochs=VALUE],
    [learning_rate=VALUE],
    [seed=VALUE],
    [overwrite={True|False}]
    )
```

"__OPTIONS__" 절에서 매개변수의 값을 기본값으로부터 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "target_col": 분류 모델의 목푯값이 담겨 있는 컬럼명 (str, default: 'target')
- "time_idx_col": 시간 인덱스 컬럼. 시계열 모델을 학습하기 위해 꼭 필요합니다 (int, default: 'time_idx')
- "max_encoder_length": 모델이 예측할 때 사용할 과거 데이터 최대 길이(time_idx) (int, default: 24)
- "min_encoder_length": 델이 예측할 때 사용할 과거 데이터의 최소 길이(time_idx). 기본값은 max_encoder_length 값에 맞춰집니다 (int, optional, default: 'max_encoder_length')
- "max_prediction_length": 최대 예측 길이. 너무 짧지 않도록 선택합니다 (int, default: 24)
- "min_prediction_length": 최소 예측 길이. 기본값은 max_encoder_length 값에 맞춰집니다 (str, optional, default: 'max_prediction_length')
- "group_id_cols": 시계열 식별자 컬럼. 데이터 세트에 여러 시계열이 존재하는 경우 해당 식별자를 리스트 형태로 기재합니다 (List[str], default: [])
- "group_normalizer": GroupNormalizer의 사용 유무. True일 경우 그룹 아이디, 타겟, 시간인덱스를 사용하여 정규화를 진행합니다 (bool, optional, default: False)
- "static_categorical_cols": 시간이 지나도 변하지 않는 범주형 변수 리스트 e.g. 제품 범주 (List[str], default: [])
- "static_real_cols": 시간이 지나도 변하지 않는 연속형 변수 리스트 e.g. 2017년 평균 인구수, 2017년 평균 가계 소득 (str, default: [])
- "time_varying_known_categorical_cols": 시간이 지남에 따라 변경되고 미래 값이 미리 알려진 범주형 변수 리스트 e.g. 공휴일 (List[str], default: [])
- "time_varying_unknown_real_cols": 시간이 지남에 따라 변경되고 미래에 알려지지 않은 연속형 변수 리스트. 여기에 타겟 값을 포함하는 것이 좋습니다 (List[str], default: [])
- "time_varying_known_real_cols": 시간이 지남에 따라 변경되고 미래에 알려진 연속 변수 리스트 (List[str], default: [])
- "time_varying_unknown_categorical_cols": 시간이 지남에 따라 변경되고 미래에 알려지지 않은 범주형 변수 리스트. 항목은 함께 인코딩되는 리스트가 될 수도 있습니다. 여기에 타겟 값을 포함하는 것이 좋습니다 e.g. 날씨 범주 (List[str], default: [])
- "special_day_cols": 동시에 여러 값을 가지는 범주형 변수 리스트 e.g. 공휴일 (List[str], default: [])
- "allow_missing_timesteps": 누락된 시간 인덱스 허용 가능 설정. True일 경우 결측 타임 인덱스에 대한 샘플이 생성됩니다. e.g 특정 시계열에 1,2,4,5에 대한 샘플만 있으면 3에 대한 샘플이 생성됩니다. 단 NA값은 처리하지 않습니다 (bool, default: False)
- "validate": 교차검증 사용옵션 (bool, optional, default: False)
- "seed": 실험의 재현성을 위해 난수 고정용 값 (int, optional, default: 42)
- "batch_size": 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 128)
- "max_epochs": 모든 학습 데이터 세트를 학습하는 횟수를 설정합니다. (int, optional, default: 30)
- "learning_rate": 모델의 학습률입니다. (float, optional, default: 3e-3)
- "overwrite": 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 여부 설정. True일 경우 기존 모델은 새로운 모델로 변경됨 (bool, optional, True|False, default: False)


## __BUILD MODEL 예시_

[미래 전력 사용량 예측하기]({/ko/tutorials/thanosql_ml/timeseries/timeseries_forecasting.ipynb/})에서 "__BUILD MODEL__" 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql
BUILD MODEL elec_predict_model
USING TFT
OPTIONS (
    target_col= 'target',
    time_idx_col='time_idx',
    group_id_cols = ['num'],
    min_encoder_length = 1,
    max_encoder_length = 840,
    min_prediction_length=1, 
    max_prediction_length = 168,
    group_normalizer = True,  
    time_varying_known_categorical_cols= ['num', 'same_temp_wind_group', 'holiday', 'dow', 'cluster', 'before_holiday_flag', 'natural_cooling_sys_flag', 'solar_sys_flag'],
    time_varying_known_real_cols=['time_idx','hour','temperature','windspeed','humidity','precipitation','insolation','days_left_holiday'],
    time_varying_unknown_real_cols=['target','log_target','mean_target','mean_target_num','mean_target_stwg','mean_target_cluster'],    
    validate = False,    
    overwrite=True,
    max_epochs = 1
)
AS 
SELECT *
FROM building_elec_train
```

## __PREDICT 구문__

"__PREDICT__" 구문을 사용하여 인공지능 모델을 적용하여 예측, 분류, 추천 등의 작업을 수행할 수 있습니다. "__PREDICT__" 구문은 "__AS__" 뒤에 나오는 query_expr을 통해 정의한 데이터 세트를 전처리할 수 있습니다.

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

__OPTIONS 절__

```sql
OPTIONS(
   [result_col=column_name]
    )
```

"__OPTIONS__" 절에서 매개변수의 값을 기본값으로부터 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "result_col": 데이터 테이블에서 예측 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'predict_result')

## __PREDICT 예시_

[미래 전력 사용량 예측하기]({/ko/tutorials/thanosql_ml/timeseries/timeseries_forecasting.ipynb/})에서 "__PREDICT__" 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql 
PREDICT USING elec_predict_model 
OPTIONS (      
        result_col = "tft_result"
        )
AS 
SELECT 
* 
FROM building_elec_test
```

## __EVALUATE 구문__

"__EVALUATE__" 구문을 사용하여 인공지능 모델에 대한 평가 작업을 수행할 수 있습니다. "__EVALUATE__" 구문은 "__AS__" 뒤에 나오는 query_expr을 통해 정의한 데이터 세트를 사용하여 모델을 평가합니다.

```sql
query_statement:
    query_expr

EVALUATE USING (model_name_expression)
AS
(query_expr)
```
## __EVALUATE 예시_

[미래 전력 사용량 예측하기]({/ko/tutorials/thanosql_ml/timeseries/timeseries_forecasting.ipynb/})에서 "__EVALUATE__" 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql  
EVALUATE USING elec_predict_model 
AS 
SELECT 
*       
FROM building_elec_test
```















<!-- 아래는 모델 구문 및 해당 옵션의 예입니다 -->
<!-- =================================== -->
<!-- MODEL SYNTAX Examples -->
<!-- BUILD -->
## __BUILD MODEL 구문__

"__BUILD MODEL__" 구문을 사용하여 인공지능 모델을 개발할 수 있습니다. "__BUILD MODEL__" 구문은 "__AS__" 뒤에 나오는 query_expr을 통해 정의된 데이터 세트를 사용하여 모델을 학습할 수 있습니다.

```sql
query_statement:
    query_expr

BUILD MODEL (model_name_expression)
USING {model_name_expression}
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```


<!-- FIT -->
## __FIT MODEL 구문__

"__FIT MODEL__" 구문을 사용하여 인공지능 모델을 재학습할 수 있습니다. "__FIT MODEL__" 구문은 "__AS__" 뒤에 나오는 query_expr을 통해 정의된 데이터 세트를 사용하여 모델을 재학습할 수 있습니다. 이 때, 재학습에 사용하는 데이터의 라벨은 기존에 학습했을 때 사용한 라벨과 같아야 합니다.

```sql
query_statement:
    query_expr

FIT MODEL (model_name_expression)
USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```


<!-- PREDICT -->
## __PREDICT 구문__

"__PREDICT__" 구문을 사용하여 인공지능 모델을 적용하여 예측, 분류, 추천 등의 작업을 수행할 수 있습니다. "__PREDICT__" 구문은 "__AS__" 뒤에 나오는 query_expr을 통해 정의한 데이터 세트를 전처리할 수 있습니다.

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


<!-- EVALUATE -->
## __EVALUATE 구문__

"__EVALUATE__" 구문을 사용하여 인공지능 모델에 대한 평가 작업을 수행할 수 있습니다. "__EVALUATE__" 구문은 "__AS__" 뒤에 나오는 query_expr을 통해 정의한 데이터 세트를 사용하여 모델을 평가합니다.

```sql
query_statement:
    query_expr

EVALUATE USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```


<!-- CONVERT -->
## __CONVERT 구문__

"__CONVERT__" 구문은 데이터를 수치화한 벡터로 변환하고 이를 사용할 데이터 테이블에 추가합니다.

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


<!-- SEARCH -->
## __SEARCH IMAGE 구문__

"__SEARCH IMAGE__" 구문을 사용하여 원하는 이미지를 검색할 수 있습니다.

```sql
query_statement:
    query_expr

SEARCH IMAGE
USING (model_name_expression)
OPTIONS(
    expression [ , ...]
    )
AS
(query_expr)
```


## __SEARCH TEXT 구문__

"__SEARCH TEXT__" 구문을 사용하여 원하는 문서를 검색할 수 있습니다.

```sql
query_statement:
    query_expr

SEARCH TEXT
USING (model_name_expression)
OPTIONS(
    expression [ , ...]
    )
AS
(query_expr)
```


## __SEARCH KEYWORD 구문__

"__SEARCH KEYWORD__" 구문을 사용하여 키워드를 추출할 수 있습니다.

```sql
query_statement:
    query_expr

SEARCH KEYWORD 
USING (model_name_expression)
OPTIONS(
    expression [ , ...]
    )
AS
(query_expr)
```


## __SEARCH VIDEO 구문__

"__SEARCH VIDEO__" 구문을 사용하여 원하는 비디오를 검색할 수 있습니다.

```sql
query_statement:
    query_expr
    
SEARCH VIDEO 
USING (model_name_expression)
OPTIONS(
    expression [ , ...]
    )
AS
(query_expr)
```




<!-- =================================== -->
<!-- OPTIONS  -->
__OPTIONS 절__
<!-- column_name, VALUE, {}, expression -->
```sql
OPTIONS(
    (text_col=column_name),
    (label_col=column_name),
    [batch_size=VALUE],
    [max_epochs=VALUE],
    [learning_rate=VALUE],
    [overwrite={True|False}]
    )
```

"__OPTIONS__" 절에서 매개변수의 값을 기본값으로부터 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

1 
<!-- Albert & Electra -->
<!-- BUILD & FIT OPTIONS -->
- "text_col": 데이터 테이블에서 학습의 대상이 될 텍스트를 담은 컬럼의 이름입니다. (str, default: 'text')
- "label_col": 데이터 테이블에서 목푯값의 정보를 담은 컬럼의 이름입니다. (str, default: 'label')
- "batch_size": 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "max_epochs": 모든 학습 데이터 세트를 학습하는 횟수를 설정합니다. (int, optional, default: 3)
- "learning_rate": 모델의 학습률입니다. (float, optional, default: 1e-4)
- "overwrite": 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (bool, optional, True|False, default: False)

<!-- PREDICT OPTIONS -->
- "text_col": 데이터 테이블에서 예측의 대상이 될 텍스트를 담은 컬럼의 이름입니다. (str, default: 'text')
- "batch_size": 한 번의 예측에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "result_col": 데이터 테이블에서 예측 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'predict_result')
- "table_name": ThanoSQL 워크스페이스 데이터베이스 내에 저장될 테이블 이름입니다. 기존에 사용한 테이블 이름으로 지정할 경우, 기존 테이블은 'predict_result' 컬럼을 추가한 테이블로 대체됩니다. 지정하지 않을 시 테이블을 저장하지 않습니다. (str, optional)

<!-- EVALUATE OPTIONS -->
- "text_col": 데이터 테이블에서 평가의 대상이 될 텍스트를 담은 컬럼의 이름입니다. (str, default: 'text')
- "label_col": 데이터 테이블에서 목푯값의 정보를 담은 컬럼의 이름입니다. (str, default: 'label')
- "batch_size": 한 번의 평가에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)


2
<!-- Wav2Vec2 -->
<!-- BUILD & FIT OPTIONS -->
- "audio_col": 데이터 테이블에서 학습의 대상이 될 오디오 파일들의 경로를 담은 컬럼을 설정합니다. (str, default: 'audio_path')
- "text_col": 데이터 테이블에서 오디오의 스크립트를 담은 컬럼을 설정합니다. (default: "text")
- "batch_size": 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "max_epochs":모든 학습 데이터 세트를 학습하는 횟수를 설정합니다. (int, optional, default: 5)
- "learning_rate": 모델의 학습률입니다. (float, optional, default: 1e-4)
- "overwrite": 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (bool, optional, True|False, default: False)

<!-- PREDICT OPTIONS -->
- "audio_col": 데이터 테이블에서 예측의 대상이 될 오디오 파일들의 경로를 담은 컬럼의 이름입니다. (str, default: 'audio_path')
- "batch_size": 한 번의 예측에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "result_col": 데이터 테이블에서 예측 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'predict_result')
- "table_name": ThanoSQL 워크스페이스 데이터베이스 내에 저장될 테이블 이름입니다. 기존에 사용한 테이블 이름으로 지정할 경우, 기존 테이블은 'predict_result' 컬럼을 추가한 테이블로 대체됩니다. 지정하지 않을 시 테이블을 저장하지 않습니다. (str, optional)

<!-- EVALUATE OPTIONS -->
- "audio_col": 데이터 테이블에서 평가의 대상이 될 오디오 파일들의 경로를 담은 컬럼의 이름입니다. (str, default: 'audio_path')
- "text_col": 데이터 테이블에서 목푯값의 정보를 담은 컬럼의 이름입니다. (str, default: 'text')
- "batch_size": 한 번의 평가에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)


3
<!-- AutoML -->
<!-- BUILD & FIT OPTIONS -->
- "target_col": 데이터 테이블에서 분류 예측 모델에 목푯값이 되는 컬럼의 이름입니다. (str, default: 'target')
- "features_to_drop": 데이터 테이블에서 학습에 이용하지 못하는 컬럼을 설정합니다. (list[str], optional)
- "impute_type": 데이터 테이블에서 빈 값(NaNs)을 처리하는 방법을 설정합니다. (str, optional, 'simple'|'iterative', default: 'simple')
> "simple": 빈 값에 대해 범주형 변수는 최빈값으로, 연속형 변수는 평균으로 처리합니다.  
> "iterative": 빈 값에 대해 나머지 속성을 통해 예측하는 알고리즘을 적용하여 처리합니다.
- "datetime_attribs": 데이터 테이블에서 날짜에 해당하는 컬럼을 설정합니다. (list[str], optional)
- "outlier_method": 데이터 테이블에서 이상치를 처리하는 방법을 설정합니다. None일 경우, 데이터 테이블은 이상치를 포함합니다. (str, optional, 'knn'|'iso'|'pca', default: None)
> "knn": K-NN 기반 접근법으로 각 데이터 사이의 거리를 기반으로 비정상 샘플을 검출합니다.  
> "iso": 주어진 데이터 테이블에 대해서 Isolation Forest를 사용하여 트리 기반으로 랜덤하게 데이터 테이블을 분기하며 모든 관측치를 고립시키며 비정상 샘플을 검출합니다. (변수가 많은 데이터 세트에서도 효율적으로 작동합니다.)  
> "pca": 주어진 데이터 테이블에 대해서 Principal Component Analysis(PCA, 주성분 분석)를 이용하여 차원을 축소하고 복원을 하는 과정을 통해 비정상 샘플을 검출합니다.
- "time_left_for_this_task": 적합한 분류 예측 모델을 찾는데 소요되는 초 단위 시간을 의미합니다. 값이 클수록 적합한 모델을 찾을 가능성이 커집니다. (int, optional, default: 60)
- "overwrite": 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (bool, optional, True|False, default: False)

<!-- PREDICT OPTIONS -->
- "result_col": 데이터 테이블에서 예측 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'predict_result')
- "table_name": ThanoSQL 워크스페이스 데이터베이스 내에 저장될 테이블 이름입니다. 기존에 사용한 테이블 이름으로 지정할 경우, 기존 테이블은 'predict_result' 컬럼을 추가한 테이블로 대체됩니다. 지정하지 않을 시 테이블을 저장하지 않습니다. (str, optional)

<!-- EVALUATE OPTIONS -->
- "target_col": 데이터 테이블에서 분류 예측 모델에 목푯값이 되는 컬럼의 이름입니다. (str, default: 'target')


4
<!-- ConvNext, EfficientNet -->
<!-- BUILD & FIT OPTIONS -->
- "image_col": 데이터 테이블에서 학습의 대상이 될 이미지의 경로를 담은 컬럼의 이름입니다. (str, default: 'image_path')
- "label_col": 데이터 테이블에서 목푯값의 정보를 담은 컬럼의 이름입니다. (str, default: 'label')
- "batch_size": 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "max_epochs": 모든 학습 데이터 세트를 학습하는 횟수를 설정합니다. (int, optional, default: 3)
- "learning_rate": 모델의 학습률입니다. (float, optional, default: 1e-3)
- "input_size": 학습에 사용할 이미지의 사이즈를 설정합니다. (int, optional)
- "color": 학습에 사용할 이미지의 색상을 설정합니다. (str, optional, ‘RGB’|'GRAY’, default: ‘RGB’)
- "overwrite": 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (bool, optional, True|False, default: False)

<!-- PREDICT OPTIONS -->
- "image_col": 데이터 테이블에서 예측의 대상이 될 이미지의 경로를 담은 컬럼의 이름입니다. (str, default: 'image_path')
- "result_col": 데이터 테이블에서 예측 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'predict_result')
- "batch_size": 한 번의 예측에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "table_name": ThanoSQL 워크스페이스 데이터베이스 내에 저장될 테이블 이름입니다. 기존에 사용한 테이블 이름으로 지정할 경우, 기존 테이블은 'predict_result' 컬럼을 추가한 테이블로 대체됩니다. 지정하지 않을 시 테이블을 저장하지 않습니다. (str, optional)
- "input_size": 예측에 사용할 이미지의 사이즈를 설정합니다. (int, optional)

<!-- EVALUATE OPTIONS -->
- "image_col": 데이터 테이블에서 평가의 대상이 될 이미지의 경로를 담은 컬럼의 이름입니다. (str, default: 'image_path')
- "label_col": 데이터 테이블에서 목푯값의 정보를 담은 컬럼의 이름입니다. (str, default: 'label')
- "batch_size": 한 번의 평가에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "input_size": 평가에 사용할 이미지의 사이즈를 설정합니다. (int, optional)


5
<!-- SimCLR -->
<!-- BUILD & FIT OPTIONS -->
- "image_col": 데이터 테이블에서 학습의 대상이 될 이미지의 경로를 담은 컬럼의 이름입니다. (str, default: 'image_path')
- "batch_size": 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 256)
- "max_epochs": 모든 학습 데이터 세트를 학습하는 횟수를 설정합니다. (int, optional, default: 5)
- "pretrained": 사전 학습된 ImageNet 가중치 사용 여부를 설정합니다. (bool, optional, True|False, default: False)
- "overwrite": 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (bool, optional, True|False, default: False)

<!-- CONVERT OPTIONS -->
- "table_name": ThanoSQL 워크스페이스 데이터베이스 내에 저장될 테이블 이름입니다. 기존에 사용한 테이블 이름으로 지정할 경우, 기존 테이블은 'convert_result' 컬럼을 추가한 테이블로 대체됩니다. 지정하지 않을 시 테이블을 저장하지 않습니다. (str, optional)
- "image_col": 데이터 테이블에서 이미지의 경로를 담은 컬럼의 이름입니다. (str, default: 'image_path')
- "result_col": 데이터 테이블에서 수치화 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'convert_result')
- "batch_size": 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 256)

<!-- SEARCH IMAGE BY IMAGE -->
- "search_by": 검색할 때 사용할 이미지|텍스트|오디오|비디오 타입을 설정합니다. (str)
- "search_input": 검색할 때 사용할 입력값입니다. (str)
- "emb_col": 데이터 테이블에서 수치화된 결과를 담은 컬럼의 이름입니다. (str)
- "result_col": 데이터 테이블에서 검색 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'search_result')



6. 
<!-- Whisper -->
<!-- PREDICT OPTIONS -->
- "audio_col": 데이터 테이블에서 예측의 대상이 될 오디오 파일들의 경로를 담은 컬럼의 이름입니다. (str, default: 'audio_path')
- "batch_size": 한 번의 예측에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "language": 오디오 파일의 주요 사용 언어를 설정합니다. ‘auto’일 경우 모델이 인식할 수 있는 99가지 언어 중 하나로 인식합니다. (str, default: 'auto')
- "task": 실행할 작업의 종류를 설정합니다. (str, 'transcribe'|'translate', default: 'transcribe')
> "transcribe": 음성 인식을 합니다.  
> "translate": 인식된 음성을 영어로 출력합니다. 이 과정은 ‘한국어 음성‘을 바로 ‘영어 텍스트‘로 번역하는 것으로, 중간에 ‘한국어 텍스트’를 거치지 않는 다는 점이 일반적인 번역 태스크와 다릅니다.
- "result_col": 데이터 테이블에서 예측 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'predict_result')
- "table_name": ThanoSQL 워크스페이스 데이터베이스 내에 저장될 테이블 이름입니다. 기존에 사용한 테이블 이름으로 지정할 경우, 기존 테이블은 'predict_result' 컬럼을 추가한 테이블로 대체됩니다. 지정하지 않을 시 테이블을 저장하지 않습니다. (str, optional)

<!-- EVALUATE OPTIONS -->
- "audio_col": 데이터 테이블에서 평가의 대상이 될 오디오 파일들의 경로를 담은 컬럼의 이름입니다. (str, default: 'audio_path')
- "batch_size": 한 번의 평가에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "language": 오디오 파일의 주요 사용 언어를 설정합니다. ‘auto’일 경우 모델이 인식할 수 있는 99가지 언어 중 하나로 인식합니다. (str, default: 'auto')
- "text_col": 데이터 테이블에서 목푯값의 정보를 담은 컬럼의 이름입니다. (str, default: 'text')


7.
<!-- ViLT -->
<!-- PREDICT OPTIONS -->
- "image_col": 데이터 테이블에서 예측의 대상이 될 이미지의 경로를 담은 컬럼의 이름입니다. (str, default: "image_path")
- "question": 예측에 사용할 질문 내용입니다. (str)
- "result_col": 데이터 테이블에서 예측 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: "predict_result")
- "table_name": ThanoSQL 워크스페이스 데이터베이스 내에 저장될 테이블 이름입니다. 기존에 사용한 테이블 이름으로 지정할 경우, 기존 테이블은 'predict_result' 컬럼을 추가한 테이블로 대체됩니다. 지정하지 않을 시 테이블을 저장하지 않습니다. (str, optional)


8. 
<!-- CLIP -->
<!-- CONVERT OPTIONS -->
- "table_name": ThanoSQL 워크스페이스 데이터베이스 내에 저장될 테이블 이름입니다. 기존에 사용한 테이블 이름으로 지정할 경우, 기존 테이블은 'convert_result' 컬럼을 추가한 테이블로 대체됩니다. 지정하지 않을 시 테이블을 저장하지 않습니다. (str, optional)
- "image_col": 데이터 테이블에서 이미지의 경로를 담은 컬럼의 이름입니다. (str, default: 'image_path')
- "text_col": 데이터 테이블에서 텍스트를 담은 컬럼의 이름입니다. (str, default: 'text')
- "convert_type": 수치화할 파일의 종류를 설정합니다. (str, 'image'|'text', default: 'image')
- "batch_size": 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "result_col": 데이터 테이블에서 수치화 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'convert_result')

<!-- SEARCH IMAGE BY TEXT -->
- "search_by": 검색할 때 사용할 이미지|텍스트|오디오|비디오 타입을 설정합니다. (str)
- "search_input": 검색할 때 사용할 입력값입니다. (str)
- "emb_col": 데이터 테이블에서 수치화된 결과를 담은 컬럼의 이름입니다. (str)
- "result_col": 데이터 테이블에서 검색 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'search_result')


9. 
<!-- XCLIP -->
<!-- CONVERT OPTIONS -->
- "table_name": ThanoSQL 워크스페이스 데이터베이스 내에 저장될 테이블 이름입니다. 기존에 사용한 테이블 이름으로 지정할 경우, 기존 테이블은 'convert_result' 컬럼을 추가한 테이블로 대체됩니다. 지정하지 않을 시 테이블을 저장하지 않습니다. (str, optional)
- "video_col": 데이터 테이블에서 비디오의 경로를 담은 컬럼의 이름입니다. (str, default: 'video_path')
- "text_col": 데이터 테이블에서 텍스트를 담은 컬럼의 이름입니다. (str, default: 'text')
- "convert_type": 수치화할 파일의 종류를 설정합니다. (str, 'video'|'text', default: 'video')
- "batch_size": 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "result_col": 데이터 테이블에서 수치화 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'convert_result')

<!-- SEARCH VIDEO BY TEXT -->
- "search_by": 검색할 때 사용할 이미지|텍스트|오디오|비디오 타입을 설정합니다. (str)
- "search_input": 검색할 때 사용할 입력값입니다. (str)
- "emb_col": 데이터 테이블에서 수치화된 결과를 담은 컬럼의 이름입니다. (str)
- "result_col": 데이터 테이블에서 검색 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'search_result')


10.
<!-- SBERT -->
<!-- BUILD & FIT OPTIONS -->
- "text_col": 데이터 테이블에서 학습의 대상이 될 텍스트를 담은 컬럼의 이름입니다. (str, default: 'text')
- "batch_size": 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "max_epochs": 모든 학습 데이터 세트를 학습하는 횟수를 설정합니다. (int, optional, default: 1)
- "learning_rate": 모델의 학습률입니다. (float, optional, default: 3e-5)
- "overwrite": 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (bool, optional, True|False, default: False)

<!-- CONVERT OPTIONS -->
- "text_col": 데이터 테이블에서 수치화의 대상이 될 텍스트를 담은 컬럼의 이름입니다. (str, default: 'text')
- "table_name": ThanoSQL 워크스페이스 데이터베이스 내에 저장될 테이블 이름입니다. 기존에 사용한 테이블 이름으로 지정할 경우, 기존 테이블은 'convert_result' 컬럼을 추가한 테이블로 대체됩니다. 지정하지 않을 시 테이블을 저장하지 않습니다. (str, optional)
- "batch_size": 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "result_col": 데이터 테이블에서 수치화 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'convert_result')

<!-- SEARCH TEXT BY TEXT -->
- "search_by": 검색할 때 사용할 이미지|텍스트|오디오|비디오 타입을 설정합니다. (str)
- "search_input": 검색할 때 사용할 입력값입니다. (str)
- "emb_col": 데이터 테이블에서 수치화된 결과를 담은 컬럼의 이름입니다. (str)
- "result_col": 데이터 테이블에서 검색 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'search_result')

<!-- SEARCH KEYWORD -->
- "lang": 사용할 언어를 설정합니다. (str, optional, 'ko'|'en' default: 'ko')
- "text_col": 데이터 테이블에서 키워드 추출의 대상이 될 텍스트를 담은 컬럼의 이름입니다. (str, default: 'text')
- "ngram_range": 키워드의 최소 단어 수와 최대 단어 수를 정합니다. (list[int, int], optional, default: [1, 2])
- "top_n": 추출할 키워드의 수입니다. 유사도가 높은 순서대로 출력합니다. (int, optional, default: 5)
- "diversity": 추출될 키워드의 다양성입니다. 높을 수록 기존에 추출된 키워드와 유사한 키워드는 다시 추출되지 않습니다. 0 <= diversity <= 1 (float, optional, default: 0.5)
- "use_stopwords": 큰 의미가 없는 단어를 제외할지 정합니다. (bool, optional, True|False, default: True)
- "threshold": 추출할 키워드의 유사도 수치의 최소값입니다. (float, optional, default: 0.0)