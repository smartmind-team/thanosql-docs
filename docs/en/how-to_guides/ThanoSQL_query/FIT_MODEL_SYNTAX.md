---
title: FIT MODEL
---

# __FIT MODEL__

## __1. FIT MODEL Statement__

The "__FIT MODEL__" statement allows users to retrain a model with new data without having any expertise in data science.

## __2. FIT MODEL Syntax__

```sql
%%thanosql
FIT MODEL [custom_model_name]
USING [built_model_name | pre-trained_AI_model_name ]
OPTIONS ([option_values_required_when_creating_an_AI_model])
AS
[dataset_to_use]
```

!!! note "__Query Details__"
    - The "__OPTIONS__" clause can change the value of a parameter. The meaning of each parameter is as follows:
        - "overwrite" : Overwrite if a model with the same name exists. If True, the existing model is overwritten with the new model. (default: False)

## __3. FIT MODEL Example__

!!! note
    - Examples are specific to one model, and the required option values ​​or the dataset used may differ from model to model. For a detailed description of each model, refer to the [ThanoSQL Pre-built Model Statement Reference](/en/how-to_guides/reference/#thanosql-pre-built-model-statement-reference)
    - Since the example works only when a specific model and dataset exist, it may not run normally when copied and used as it is.

```sql
%%thanosql
FIT MODEL mynewmodel
USING myoldmodel
OPTIONS (
    target = 'survived',
    impute_type='simple',
    features_to_drop=["name", 'ticket', 'passengerid', 'cabin'],
    overwrite = True
    )
AS
SELECT *
FROM titanic_train
```