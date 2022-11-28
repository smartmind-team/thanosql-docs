---
title: TRANSFORM
---

# __TRANSFORM__

## __1. TRANSFORM Statement__

The "__TRANSFORM__" statement allows users to apply the same data preprocessing method used to create a model to the test dataset.

## __2. TRANSFORM Syntax__

```sql
%%thanosql
TRANSFORM USING [built_model_name]
AS
[test_dataset_to_use]
```

## __3. TRANSFORM Example__

!!! note
    - Examples are specific to one model, and the required option values ​​or the dataset used may differ from model to model. For a detailed description of each model, refer to the [ThanoSQL Pre-built Model Statement Reference](/en/how-to_guides/reference/#thanosql-pre-built-model-statement-reference)
    - Since the example works only when a specific model and dataset exist, it may not run normally when copied and used as it is.

```sql
%%thanosql
TRANSFORM USING mymodel 
AS 
SELECT * 
FROM titanic_test 
```
