---
title: Temporal Fusion Transformer(TFT)
---

# __Temporal Fusion Transformer(TFT)__

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
OPTIONS (
    (target_col=column_name),
    (time_idx_col=column_name),
    (max_encoder_length=VALUE),
    [min_encoder_length=VALUE],
    (max_prediction_length=VALUE),
    [min_prediction_length=VALUE],
    (group_id_cols=[column_name, ...]),
    [group_normalizer={True|False}]
    (static_categorical_cols=[column_name, ...]),
    (static_real_cols=[column_name, ...]),
    (time_varying_known_categorical_cols=[column_name, ...]),
    (time_varying_known_real_cols=[column_name, ...]),
    (time_varying_unknown_categorical_cols=[column_name, ...]),
    (time_varying_unknown_real_cols=[column_name, ...]),
    [special_day_cols=[column_name, ...]],
    (allow_missing_timesteps={True|False}),
    [validate={True|False}],
    [seed=VALUE],
    [batch_size=VALUE],
    [max_epochs=VALUE],
    [learning_rate=VALUE],
    [overwrite={True|False}]
    )
```

"__OPTIONS__" 절에서 매개변수의 값을 기본값으로부터 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "target_col": 데이터 테이블에서 시계열 예측 모델의 목푯값이 되는 컬럼의 이름입니다. (str, default: 'target')
- "time_idx_col": 시간 인덱스 컬럼입니다. 시계열 모델을 학습하기 위해 꼭 필요합니다. (int, default: 'time_idx')
- "max_encoder_length": 모델이 예측할 때 사용할 과거 데이터 최대 길이(time_idx)입니다. (int, default: 24)
- "min_encoder_length": 모델이 예측할 때 사용할 과거 데이터의 최소 길이(time_idx)입니다. 기본값은 max_encoder_length 값에 맞춰집니다. (int, optional)
- "max_prediction_length": 최대 예측 길이입니다. 너무 짧지 않도록 선택합니다. (int, default: 24)
- "min_prediction_length": 최소 예측 길이입니다. 기본값은 max_encoder_length 값에 맞춰집니다. (int, optional)
- "group_id_cols": 시계열 식별자 컬럼입니다. 데이터 세트에 여러 시계열이 존재하는 경우 해당 식별자를 리스트 형태로 기재합니다. (List[str])
- "group_normalizer": GroupNormalizer의 사용 유무를 설정합니다. True일 경우 그룹 아이디, 타겟, 시간 인덱스를 사용하여 정규화를 진행합니다. (bool, optional, True|False, default: False)
- "static_categorical_cols": 시간이 지나도 변하지 않는 범주형 변수 리스트입니다. e.g. 제품 범주 (List[str])
- "static_real_cols": 시간이 지나도 변하지 않는 연속형 변수 리스트입니다. e.g. 2017년 평균 인구수, 2017년 평균 가계 소득 (str)
- "time_varying_known_categorical_cols": 시간이 지남에 따라 변경되고 미래 값이 미리 알려진 범주형 변수 리스트입니다. e.g. 공휴일 (List[str])
- "time_varying_unknown_real_cols": 시간이 지남에 따라 변경되고 미래에 알려지지 않은 연속형 변수 리스트입니다. 여기에 타겟 값을 포함하는 것이 좋습니다. (List[str])
- "time_varying_known_real_cols": 시간이 지남에 따라 변경되고 미래에 알려진 연속 변수 리스트입니다. (List[str])
- "time_varying_unknown_categorical_cols": 시간이 지남에 따라 변경되고 미래에 알려지지 않은 범주형 변수 리스트입니다. 항목은 함께 인코딩되는 리스트가 될 수도 있습니다. 여기에 타겟 값을 포함하는 것이 좋습니다. e.g. 날씨 범주 (List[str])
- "special_day_cols": 동시에 여러 값을 가지는 범주형 변수 리스트입니다. e.g. 공휴일 (List[str], optional)
- "allow_missing_timesteps": 누락된 시간 인덱스 허용 가능 설정입니다. True일 경우 결측 타임 인덱스에 대한 샘플이 생성됩니다. e.g 특정 시계열에 1,2,4,5에 대한 샘플만 있으면 3에 대한 샘플이 생성됩니다. 단 NA값은 처리하지 않습니다. (bool, True|False, default: False)
- "validate": 교차검증 사용옵션을 설정합니다. (bool, optional, True|False, default: False)
- "seed": 실험의 재현성을 위해 난수 고정용 값입니다. (int, optional, default: 42)
- "batch_size": 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 128)
- "max_epochs": 모든 학습 데이터 세트를 학습하는 횟수를 설정합니다. (int, optional, default: 30)
- "learning_rate": 모델의 학습률입니다. (float, optional, default: 3e-3)
- "overwrite": 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 여부 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (bool, optional, True|False, default: False)

__BUILD MODEL 예시__

[시계열 예측 모델 만들기](/ko/tutorials/thanosql_ml/timeseries/timeseries_forecasting.ipynb/)에서 "__BUILD MODEL__" 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql
BUILD MODEL elec_predict_model
USING TFT
OPTIONS (
    target_col='target',
    time_idx_col='time_idx',
    group_id_cols=['num'],
    min_encoder_length=1,
    max_encoder_length=840,
    min_prediction_length=1,
    max_prediction_length=168,
    group_normalizer=True,
    time_varying_known_categorical_cols=['num','same_temp_wind_group','holiday','dow','cluster','before_holiday_flag','natural_cooling_sys_flag','solar_sys_flag'],
    time_varying_known_real_cols=['time_idx','hour','temperature','windspeed','humidity','precipitation','insolation','days_left_holiday'],
    time_varying_unknown_real_cols=['target','log_target','mean_target','mean_target_num','mean_target_stwg','mean_target_cluster'],
    validate=False,
    overwrite=True,
    max_epochs=1
    )
AS
SELECT *
FROM building_elec_train
```

## __PREDICT 구문__

"__PREDICT__" 구문을 사용하여 인공지능 모델을 적용하여 예측, 분류, 추천 등의 작업을 수행할 수 있습니다. "__PREDICT__" 구문은 "__AS__" 뒤에 나오는 query_expr을 통해 정의한 데이터 세트를 전처리할 수 있습니다.

!!! warning "테스트 데이터 세트 전처리 필요"
    - 시계열 모델을 사용하여 예측하기 위해서는 설정한 인코더 길이 만큼의 정답이 포함된 학습용 데이터를 테스트 데이터 세트 앞에 병합해야 합니다. e.g 만약 모델 인코더 길이를 860으로 설정하고 테스트 데이터의 time_idx 값이 학습용 데이터에 이어 1000부터 시작한다면 time_idx 140 ~ 999의 데이터를 테스트 데이터 세트 앞에 병합해야 합니다.

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
OPTIONS (
   [result_col=column_name]
    )
```

"__OPTIONS__" 절에서 매개변수의 값을 기본값으로부터 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "result_col": 데이터 테이블에서 예측 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'predict_result')

__PREDICT 예시__

[시계열 예측 모델 만들기](/ko/tutorials/thanosql_ml/timeseries/timeseries_forecasting.ipynb/)에서 "__PREDICT__" 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql
PREDICT USING elec_predict_model
OPTIONS (
    result_col='tft_result'
    )
AS
SELECT *
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
__EVALUATE 예시__

[시계열 예측 모델 만들기](/ko/tutorials/thanosql_ml/timeseries/timeseries_forecasting.ipynb/)에서 "__EVALUATE__" 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql
EVALUATE USING elec_predict_model
AS
SELECT *
FROM building_elec_test
```