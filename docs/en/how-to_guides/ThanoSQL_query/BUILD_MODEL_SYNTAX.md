---
title: BUILD MODEL 
---

# __BUILD MODEL__

## __1. BUILD MODEL Statement__

The "__BUILD MODEL__" statement enables users to create a desired AI model without any expertise in data science. 

## __2. BUILD MODEL Syntax__

```sql
%%thanosql
BUILD MODEL [custom_model_name]
USING [AI_model_to_use]
OPTIONS ([option_values_​​required_when_creating_an_AI_model])
AS
[dataset_to_use]

```

!!! note "__Query Details__"
    - The "__OPTIONS__" clause can change the value of a parameter. The meaning of each parameter is as follows:
        - "overwrite" : Overwrite if a model with the same name exists. If True, the existing model is overwritten with the new model. (DEFAULT: False)

## __3. BUILD MODEL Example__

!!! note
    - Examples are specific to one model, and the required option values ​​or the dataset used may differ from model to model. For a detailed description of each model, refer to the [ThanoSQL Pre-built Model Statement Reference](/en/how-to_guides/reference/#thanosql-pre-built-model-statement-reference)
    - Since the example works only when a specific model and dataset exist, it may not run normally when copied and used as it is.

### __3-1. Using the Auto_ML Model to Create a Classification Model__

```sql
%%thanosql
BUILD MODEL mymodel
USING AutomlClassifier
OPTIONS (
    target='survived',
    impute_type='iterative',
    features_to_drop=["name", 'ticket', 'passengerid', 'cabin'],
    overwrite = True
    )
AS
SELECT *
FROM titanic_train
```

!!! note "AI models that can be used with '__BUILD MODEL__ statement'"
    - Auto-ML Classification model - AutomlClassifier
    - Auto-ML Regression model - AutomlRegressor
    - ConvNeXT Model - ConvNeXt_Tiny, ConvNeXt_Base
    - EfficientNet Model - EfficientNetV2S, EfficientV2M
    - Albert Model - AlbertKo, AlbertEn
    - Electra Model - ElectraKo, ElectraEn
    - Wav2Vec2 Model - Wav2Vec2Ko, Wav2Vec2En
    - SBERT Model - SBERTKo, SBERTEn