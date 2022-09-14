---
title: FIT MODEL
---

# __FIT MODEL__

## __1. Overview__

The "__FIT MODEL__" statement allows users to retrain an AI model with new data without any expertise in data science.

## __2. Syntax__

```sql
%%thanosql
FIT MODEL [custom_model_name]
USING [built_model_name | pre-trained_AI_model_name ]
OPTIONS ([option_values_required_when_creating_an_AI_model])
AS
[dataset_to_use]
```

!!! NOTE
    Option values used in the "__OPTIONS__" clause are different for each AI model. Specific values needed for each model are listed [here](/en/how-to_guides/OPTIONS/). 

!!! warning
    The Auto-ML model is retrained by exclusively switching the dataset instead of changing the parameter values of the AI model to keep consistency with option values in the "__OPTIONS__" clause.
## __3. Example__
The example below demonstrates a method to create a <mark style="background-color:#E9D7FD ">fit_test_classifier</mark> model by retraining the <mark style="background-color:#E9D7FD ">test_classifier</mark> model with a new dataset and a "__FIT_MODEL__" statement. 

```sql
%%thanosql
FIT MODEL fit_test_classifier
USING test_classifier
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
