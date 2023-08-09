---
title: Temporal Fusion Transformer(TFT)
---

# __Temporal Fusion Transformer(TFT)__

__Notation Conventions__

- Parentheses `()` indicate ^^literal^^ parentheses.
- Braces `{}` are used to bind combinations of options.
- The bracket `[]` indicates an optional clause.
- An ellipsis following a comma in brackets [,...] means that the preceding item can be repeated as a comma-separated list
- The vertical bar `|` represents the logic `OR`.
- VALUE represents a regular value.

!!! note ""
    - __literal__: a fixed or unchangeable value, also known as a Constant.
    > Each literal has a special data type such as column, in the table.

## __BUILD MODEL Syntax__

Use the "__BUILD MODEL__" statement to develop an AI model. The "__BUILD MODEL__" statement allows you to train a model using datasets defined with the query_expr that comes after the "__AS__" clause. 

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

__OPTIONS Clause__

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

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

- "target_col": a column representing the target, which can be categorical or continuous (str, default: 'target')
- "time_idx_col": an integer column representing the time index. This column is used to determine the sequence of samples. If there are no missing observations, the time index should increase by 1 for each subsequent sample. The first 'time_idx' for each series does not necessarily have to be 0, but any value is allowed (str, default: 'time_idx')
- "max_encoder_length": maximum length to encode. This is the maximum history length used by the time series dataset (int, default: 24)
- "min_encoder_length": minimum length to encode. Defaults to 'max_encoder_length' (int, optional, default: 'max_encoder_length')
- "max_prediction_length": the maximum prediction/decoder length (this should not be too short because it can aid in convergence) (int, default: 24)
- "min_prediction_length": the minimum prediction/decoder length. Defaults to 'max_prediction_length' (int, optional, default: 'max_prediction_length')
- "group_id_cols": list of columns identifying a time series. Together with the time_idx, the group_ids identify a sample. Set this to the constant column name. If you only have one timeseries. (List[str])
- "group_normalizer": if True, a transformer that takes group_ids, target, and time_idx to normalize targets (bool, optional, True|False, default: False)
- "static_categorical_cols": list of categorical variables that do not change over time; entries can also be lists, which are then encoded together (e.g. useful for product categories) (List[str])
- "static_real_cols": list of continuous variables that do not change over time (str)
- "time_varying_known_categorical_cols": list of categorical variables that change over time and are known in the future; entries can also be lists, which are then encoded together (e.g. useful for special days or promotion categories) (List[str])
- "time_varying_unknown_real_cols": list of continuous variables that change over time and are not known in the future. You might want to include your target here (List[str])
- "time_varying_known_real_cols": list of continuous variables that change over time and are known in the future (e.g. price of a product, but not demand of a product) (List[str])
- "time_varying_unknown_categorical_cols": list of categorical variables that change over time and are not known in the future; entries can also be lists, which are then encoded together (e.g. useful for weather categories). You might want to include your target here. (List[str])
- "special_day_cols": a list of categorical variables will be combined with a column name of 'speical_days'. This is particularly useful if a categorical variable can have multiple values at the same time. An example are holidays which can be overlapping. (List[str], optional)
- "allow_missing_timesteps": if to allow missing timesteps that are automatically filled up. Missing values refer to gaps in the time_idx, e.g. if a specific timeseries has only samples for 1, 2, 4, 5, the sample for 3 will be generated on-the-fly. Allow missings does not deal with NA values. You should fill NA values before passing the dataframe to the TimeSeriesDataSet (bool, True|False, default: False)
- "validate": if true, cross validation will be implemented (bool, optional, True|False, default: False)
- "seed": a number that is used to initialize the pseudorandom number generator. It can have a huge impact on the training results. This lets a model be reproducible (int, optional, default: 42)
- "batch_size": the size of the dataset bundle utilized in a single cycle of training (int, optional, default: 128)
- "max_epochs": number of times to train with the training dataset (int, optional, default: 30)
- "learning_rate": the learning rate of the model (float, optional, default: 3e-3)
- "overwrite": determines whether to overwrite a model if it already exists. If set as True, the old model is replaced with the new model (bool, optional, True|False, default: False)


__BUILD MODEL Example__

An example "__BUILD MODEL__" query can be found in [Electricity Consumption Forecasting](../../../tutorials/thanosql_ml/timeseries/timeseries_forecasting/).

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

## __PREDICT Syntax__

Use the "__PREDICT__" statement to apply AI models to perform prediction, classification, recommendation, and more. The "__PREDICT__" statement can preprocess the dataset defined by the query_expr that comes after the "__AS__" clause.

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
__OPTIONS Clause__

```sql
OPTIONS(
   [result_col=column_name]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

- "result_col": the column that contains the predicted results (str, optional, default: 'predict_result')

__PREDICT Example__

An example "__PREDICT__" query can be found in [Electricity Consumption Forecasting](../../../tutorials/thanosql_ml/timeseries/timeseries_forecasting/).

!!! warning "Test dataset requires preprocessing"
    - To predict using a time series model, you must merge the train dataset with the configured encoder length as an answer to the test dataset. e.g. If you set the model encoder length to 860 and the test dataset's "time_idx" starts at 1000, the "time_idx" of 140–999 data points should be merged into the test dataset.
    
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

## __EVALUATE Syntax__

Use the "__EVALUATE__" statement to evaluate the AI model. The "__EVALUATE__" expression evaluates a model using the dataset defined by the query_expr that comes after the "__AS__" clause.

```sql
query_statement:
    query_expr

EVALUATE USING (model_name_expression)
AS
(query_expr)
```

__EVALUATE Example__

An example "__EVALUATE__" query can be found in [Electricity Consumption Forecasting](../../../tutorials/thanosql_ml/timeseries/timeseries_forecasting/).

```sql
%%thanosql
EVALUATE USING elec_predict_model
AS
SELECT *
FROM building_elec_test
```