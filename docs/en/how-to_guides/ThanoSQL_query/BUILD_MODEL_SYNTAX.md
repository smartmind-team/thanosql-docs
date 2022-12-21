---
title: BUILD MODEL 
---

# __BUILD MODEL__

## __1. BUILD MODEL Statement__

The "__BUILD MODEL__" statement enables users to create a desired AI model without any expertise in data science. 

## __2. BUILD MODEL Syntax__

```sql
query_statement:
    query_expr

BUILD MODEL (model_name_expression)
USING {model_name_expression}
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

!!! note "__Query Details__"
    - The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows:
        - "overwrite": determines whether to overwrite a model if it already exists. If set as True, the old model is replaced with the new model (bool, optional, True|False, default: False)

## __3. BUILD MODEL Example__

!!! note
    - Examples are specific to one model, and the required option values ​​or the dataset used may differ from model to model. For a detailed description of each model, refer to the [ThanoSQL Model Statement Reference](/en/how-to_guides/reference/#thanosql-model-statement-reference)
    - Because the example only works when a specific model and dataset are present, it may not run normally if copied and used as is.

An example "__BUILD MODEL__" query can be found in  [Create a Classification Model Using AutoML](/en/tutorials/Thanosql_ml/classification/automl_classification/).

```sql
%%thanosql
BUILD MODEL titanic_classification
USING AutomlClassifier
OPTIONS (
    target='survived',
    impute_type='iterative',
    features_to_drop=["name", 'ticket', 'passengerid', 'cabin'],
    outlier_method='pca',
    overwrite=True
    )
AS
SELECT *
FROM titanic_train
```

!!! note "AI models that can be used with '__BUILD MODEL__ statement'"
    - AutoML Classification model - AutomlClassifier
    - AutoML Regression model - AutomlRegressor
    - ConvNeXT Model - ConvNeXt_Tiny, ConvNeXt_Base
    - EfficientNet Model - EfficientNetV2S, EfficientV2M
    - Albert Model - AlbertKo, AlbertEn
    - Electra Model - ElectraKo, ElectraEn
    - Wav2Vec2 Model - Wav2Vec2Ko, Wav2Vec2En
    - SimCLR Model - SimCLR
    - SBERT Model - SBERTKo, SBERTEn