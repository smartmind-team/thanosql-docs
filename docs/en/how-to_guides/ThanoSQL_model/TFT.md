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

The "__BUILD MODEL__" statement {brief explanation of the syntax}

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
    (time_varying_unknown_real_cols:VALUE),
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

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

- "target_col": column denoting the target or list of columns denoting the target - categorical or continous (str, default: 'target')
- "time_idx_col": integer column denoting the time index. This columns is used to determine the sequence of samples. If there no missings observations, the time index should increase by +1 for each subsequent sample. The first time_idx for each series does not necessarily have to be 0 but any value is allowed (int, default: 'time_idx')
- "max_encoder_length": maximum length to encode. This is the maximum history length used by the time series dataset (int, default: 24)
- "min_encoder_length": minimum allowed length to encode. Defaults to max_encoder_length (int, optional, default: 'max_encoder_length')
- "max_prediction_length": maximum prediction/decoder length (choose this not too short as it can help convergence) (int, default: 24)
- "min_prediction_length": minimum prediction/decoder length. Defaults to max_prediction_length (str, optional, default: 'max_prediction_length')
- "group_id_cols": list of column names identifying a time series. This means that the group_ids identify a sample together with the time_idx. If you have only one timeseries, set this to the name of column that is constant (List[str], default: [])
- "group_normalizer": if True, transformer that take group_ids, target and time_idx to normalize targets (bool, optional, default: False)
- "static_categorical_cols": list of categorical variables that do not change over time, entries can be also lists which are then encoded together (e.g. useful for product categories) (List[str], default: [])
- "static_real_cols": list of continuous variables that do not change over time (str, default: [])
- "time_varying_known_categorical_cols": list of categorical variables that change over time and are known in the future, entries can be also lists which are then encoded together (e.g. useful for special days or promotion categories) (List[str], default: [])
- "time_varying_unknown_real_cols": list of continuous variables that change over time and are not known in the future. You might want to include your target here (List[str], default: [])
- "time_varying_known_real_cols": list of continuous variables that change over time and are known in the future (e.g. price of a product, but not demand of a product) (List[str], default: [])
- "time_varying_unknown_categorical_cols": list of categorical variables that change over time and are not known in the future, entries can be also lists which are then encoded together (e.g. useful for weather categories). You might want to include your target here. (List[str], default: [])
- "special_day_cols": list of categorical variables will be combined with a column name of 'speical_days'. This is particularly useful if a categorical variable can have multiple values at the same time. An example are holidays which can be overlapping. (List[str], default: [])
- "allow_missing_timesteps": if to allow missing timesteps that are automatically filled up. Missing values refer to gaps in the time_idx, e.g. if a specific timeseries has only samples for 1, 2, 4, 5, the sample for 3 will be generated on-the-fly. Allow missings does not deal with NA values. You should fill NA values before passing the dataframe to the TimeSeriesDataSet (bool, default: False)
- "validate": if true, cross validation will be implemented (bool, optional, default: False)
- "seed": a number that is used to initialize the pseudorandom number generator. It can have a huge impact on the training results. This let a model be reproducible (int, optional, default: 42)
- "batch_size" is the size of dataset bundle utilized in a single cycle of training (int, optional, default: 128)
- "max_epochs": number of times to train with the training dataset (int, optional, default: 30)
- "learning_rate": the learning rate of the model (float, optional, default: 3e-3)
- "overwrite": determines whether to overwrite a model if it already exists. If set as True, the old model is replaced with the new model (bool, optional, True|False, default: False) (bool, optional, True|False, default: False)


__BUILD MODEL Example__

An example "__BUILD MODEL__" query can be found in [Electrictity Consumption Forecasting]({/en/tutorials/thanosql_ml/timeseries/timeseries_forecasting.ipynb/}).

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

## __PREDICT Example___

An example "__PREDICT__" query can be found in [Electrictity Consumption Forecasting]({/en/tutorials/thanosql_ml/timeseries/timeseries_forecasting.ipynb/}).

```sql
%%thanosql 
PREDICT USING elec_predict_model 
OPTIONS (      
    result_col="tft_result"
    )
AS 
SELECT 
* 
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

## __EVALUATE Example___

An example "__EVALUATE__" query can be found in [Electrictity Consumption Forecasting]({/en/tutorials/thanosql_ml/timeseries/timeseries_forecasting.ipynb/}).

```sql
%%thanosql  
EVALUATE USING elec_predict_model 
AS 
SELECT 
*       
FROM building_elec_test
```