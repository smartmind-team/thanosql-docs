---
title: {statement name}
---

# __{statement name}__

## __1. {statement name} Statement__

The "__{statement name}__" statement {explanation}.

## __2. {statement name} Syntax__

```sql
{statement query syntax
e.g. 
query_statement:
    query_expr

BUILD MODEL (model_name_expression)
USING {model_name_expression}
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
}

```

!!! note "__Query Details__"
    {query details explanation
    e.g.
    - The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows:
        - "overwrite": determines whether to overwrite a model if it already exists. If set as True, the old model is replaced with the new model (bool, optional, True|False, default: False)
    }

## __3. {statement name} Example__

!!! note
    - Examples are specific to one model, and the required option values ​​or the dataset used may differ from model to model. For a detailed description of each model, refer to the [ThanoSQL Model Statement Reference](/en/how-to_guides/reference/#thanosql-model-statement-reference)
    - Because the example only works when a specific model and dataset are present, it may not run normally if copied and used as is.

```sql
%%thanosql
{statement query example
e.g.
BUILD MODEL titanic_automl_classification
USING AutomlClassifier
OPTIONS (
    target_col='survived',
    impute_type='iterative',
    features_to_drop=['name', 'ticket', 'passengerid', 'cabin'],
    time_left_for_this_task=300,
    overwrite=True
    )
AS
SELECT *
FROM titanic_train
}
```

{note about what models this particular ThanoSQL clause can be used with 
e.g.
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
}


<!-- EXAMPLES OF Query Details  -->
<!-- BUILD and more -->
!!! note "__Query Details__"
    - The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows:
        - "overwrite": determines whether to overwrite a model if it already exists. If set as True, the old model is replaced with the new model (bool, optional, True|False, default: False)

<!-- CONVERT, PREDICT -->
!!! note "__Query Details__"
    - The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows:
        - "table_name": the table name to be stored in the ThanoSQL workspace database. If a previously used table is specified, the existing table will be replaced by the new table with a '{predict_result | convert_result}' column. If not specified, the result dataframe will not be saved as a data table (str, optional)

<!-- COPY -->
!!! note "__Query Details__"
    - The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows:
        - "overwrite": determines whether to overwrite a table if it already exists. If set as True, the old table is replaced with the new table (bool, optional, True|False, default: False)