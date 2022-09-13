---
title: BUILD MODEL 
---

# __BUILD MODEL__

## __1. BUILD MODEL Syntax Overview__

Users can simply use the "__BUILD MODEL__" syntax to develop the desired AI model without the need for data science expertise.

## __2. BUILD MODEL Syntax__

```sql
%%thanosql
BUILD MODEL [Custom_Model_Name]
USING [AI_model_to_use]
OPTIONS ([Option_values_​​required_when_creating_an_AI_model])
AS
[data_set_to_use]

```

!!! NOTE
    "The option values used by __OPTIONS__" apply differently depending on the AI model you are using. The "__OPTIONS__" for each AI model is available [in the options documentation page.](/en/how-to_guides/OPTIONS/)

## __3. BUILD MODEL Syntax example__

### __3-1. Using Auto_ML Models to Create Classification Models__

The example below uses the syntax "__BUILD MODEL__" to create a classification model using a user-defined <mark style="background-color:#E9D7FD ">Titanic_classification</mark> model provided by ThanoSQL ["AutomlClassifier"](https://www.automl.org/automl/).

If you're curious about the whole process, try [Creating a Titanic Survivor Classification Model using Auto-ML](/en/tutorials/thanosql_ml/classification/automl_classification/).

```sql
%%thanosql
BUILD MODEL titanic_classification
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

!!! note "Artificial intelligence models available with '__BUILD MODEL__'"
    - Auto-ML Classification model - AutomlClassifier
    - Auto-ML Regression model - AutomlRegressor
    - ConvNeXT Model - ConvNeXt_Tiny , ConvNeXt_Base
    - EfficientNet Model - EfficientNetV2S , EfficientV2M
    - Albert Model - AlbertKo, AlbertEn
    - Electra Model - ElectraKo , ElectraEn
    - Wav2Vec2 Model - Wav2Vec2Ko , Wav2Vec2En
