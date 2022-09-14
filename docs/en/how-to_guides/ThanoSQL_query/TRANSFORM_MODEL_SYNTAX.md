---
title: TRANSFORM
---

# __TRANSFORM__

## __1. Overview__

The "__TRANSFORM__" statement allows users to apply the same data preprocessing method used to create a model to the test dataset.

## __2. Syntax__

```sql
%%thanosql
TRANSFORM USING [built_model_name]
AS
[test_dataset_to_use]
```

## __3. Example__
The example below demonstrates how to use a "__TRANSFORM__" statement to apply the same data preprocessing to the <mark style="background-color:#FFEC92 ">titanic_test</mark> dataset as the <mark style="background-color:#E9D7FD ">test_classifier</mark>  model built in [Learning Models](/en/how-to_guides/ThanoSQL_ml/BUILD_MODEL_SYNTAX/).

```sql
%%thanosql
TRANSFORM USING test_classifier
AS
SELECT *
FROM titanic_test
```
