---
title: PREDICT
---

# __PREDICT__

## __1. PREDICT Statement__

The "__PREDICT__" statement allows users to apply models to perform prediction, classification, recommendation, and more.


## __2. PREDICT Syntax__

```sql
%%thanosql
PREDICT USING [built_model_name]
OPTIONS ([option_values_required_for_inference])
AS
[test_dataset_to_use]
```

!!! note "__Query Details__"
    - The "__OPTIONS__" clause can change the value of a parameter. The meaning of each parameter is as follows:
        - "table_name": the name of the new table to be created.

## __3. PREDICT Example__

!!! note
    - Examples are specific to one model, and the required option values ​​or the dataset used may differ from model to model. For a detailed description of each model, refer to the [ThanoSQL Model Statement Reference](/en/how-to_guides/reference/#thanosql-model-statement-reference)
    - Since the example works only when a specific model and dataset exist, it may not run normally when copied and used as it is.

```sql
%%thanosql
PREDICT USING mymodel
OPTIONS (
    table_name='predict_result_table'
    text_col='review'
    )
AS
SELECT *
FROM movie_review_test```
```