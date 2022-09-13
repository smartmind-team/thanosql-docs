---
title: PREDICT
---

# __PREDICT__

## __1. PREDICT Syntax overview__

Using the "__PREDICT__" syntax, users can apply AI models to test datasets to perform prediction, classification, recommendation, and more.

## __2. PREDICT Syntax__

```sql
%%thanosql
PREDICT USING [Existing_trained_model_name]
OPTIONS ([Option_value_required_for_inference_by_model])
AS
[test_data_set_to_use]
```

## __3. PREDICT Syntax example__

You can find examples of using the algorithm query syntax in the [Create a text classification model](/en/tutorials/thanosql_ml/classification/classification_electra/).

````sql
%%thanosql
PREDICT USING my_movie_review_classifier
OPTIONS (
    text_col='review'
    )
AS
SELECT *
FROM movie_review_test```
````

!!! note "__Query Details__"
    - "__OPTIONS__" A clause can change the value of a parameter from its default value in an image model. The meaning of each parameter is as follows.
        - "text_col" : Sets the column containing the text to be classified in the data table.(DEFAULT : "text")
