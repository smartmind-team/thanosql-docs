---
title: EVALUATE
---

# __EVALUATE__

## __1. EVALUATE Syntax Overview__

The "__EVALUATE__" syntax allows users to evaluate performance on AI models.

## __2. EVALUATE Syntax__

```sql
%%thanosql
EVALUATE USING [Existing_trained_model_name]
OPTIONS ([Option_value_required_for_evaluation_by_model])
AS
[data_set_to_use]
```

!!! warning
    If the data set you want to use does not have a target, you cannot evaluate the performance of the model.

## __3. EVALUATE Syntax example__

The example below uses the syntax "__EVALUATE__" to evaluate the <mark style="background-color:#E9D7FD ">test_classifier</mark> classification model that you created in [BUILD MODEL](/en/how-to_guides/ThanoSQL_ml/BUILD_MODEL_SYNTAX/).

```sql
%%thanosql
EVALUATE USING test_classifier
OPTIONS (
    target='survived'
    )
AS
SELECT *
FROM titanic_train
```

[![IMAGE](/img/thanosql_ml/classification/automl_classification/img2.png)](/img/thanosql_ml/classification/automl_classification/img2.png)

!!! note "__Query Details__"  
    Evaluate a model called <mark style="background-color:#E9D7FD ">titanic_classification</mark> built using the "__EVALUATE__" query syntax.
    For "target" in "__OPTIONS__", write down the name of the column(<mark style="background-color:#D7D0FF">survived</mark>) being the target value in the classification prediction model.
