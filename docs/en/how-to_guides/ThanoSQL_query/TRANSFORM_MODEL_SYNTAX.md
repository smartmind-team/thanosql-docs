---
title: TRANSFORM
---

# __TRANSFORM__

## __1. TRANSFORM Syntax overview__

The "__TRANSFORM__" syntax allows the user to apply the same preprocessing method used to create an AI model to the test dataset.

## __2. TRANSFORM Syntax__

```sql
%%thanosql
TRANSFORM USING [Existing_trained_model_name]
AS
[test data set to use]
```

## __3. TRANSFORM Syntax eample__

The example below uses the syntax "__TRANSFORM__" to apply the same processing to the <mark style="background-color:#FFEC92 ">titanic_test</mark> dataset as the <mark style="background-color:#E9D7FD ">test_classifier</mark> classification model that you created in [Learning Models](/en/how-to_guides/ThanoSQL_ml/BUILD_MODEL_SYNTAX/).

```sql
%%thanosql
TRANSFORM USING test_classifier
AS
SELECT *
FROM titanic_test
```
