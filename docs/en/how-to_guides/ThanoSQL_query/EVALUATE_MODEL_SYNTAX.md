---
title: EVALUATE
---

# __EVALUATE__

## __1. EVALUATE Statement__

The "__EVALUATE__" statement allows users to evaluate performance of their models.

## __2. EVALUATE Syntax__ 

```sql
%%thanosql
EVALUATE USING [built_model_name]
OPTIONS ([option_value_required_for_evaluation])
AS
[dataset_to_use]
```

!!! warning
    - If the dataset you want to use does not have a target, you cannot evaluate the performance of the model.

## __3. EVALUATE Example__

!!! note
    - Examples are specific to one model, and the required option values ​​or the dataset used may differ from model to model. For a detailed description of each model, refer to [ThanoSQL Pre-built Model Statement Reference](/en/how-to_guides/reference/#thanosql-pre-built-model-statement-reference)
    - Since the example works only when a specific model and dataset exist, it may not run normally when copied and used as it is.

```sql
%%thanosql
EVALUATE USING mymodel
OPTIONS (
    target='survived'
    )
AS
SELECT *
FROM titanic_train
```

!!! faq "__Metrics__"
    - Depending on the situation, metrics can differ for each model. For example, simple classification with two target values and multiple classification with three or more target values will use different evaluation metrics such as the ones listed below.


| Model      | Metrics                     |
| :-----------: | :-----------------------------------------------: |
| `AutomlClassifier` (simple classification) | Accuracy, ROCAUC, Recall, Precision, f1-score, Kappa, MCC  |
| `AutomlClassifier` (multiple classification)       | Accuracy, macro-Recall, macro-Precision, macro-f1-score|
| `AutomlRegressor`    | MAE, MSE, R2, RMSLE, MAPE|