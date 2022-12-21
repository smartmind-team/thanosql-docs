---
title: UPLOAD MODEL
---

# __UPLOAD MODEL__

## __1. UPLOAD MODEL Statement__

The "__UPLOAD MODEL__" statement allows users to upload their custom models to be used within the ThanoSQL environment. 

## __2. UPLOAD MODEL Syntax__

```sql
%%thanosql
UPLOAD MODEL (model_name_expression)
OPTIONS (
    framework=[model_framework],
    overwrite=True
    ) 
FROM [model_path_expression]
```

!!! note "__Query Details__"
    - The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows:
        - "framework": column that contains the model's target value (str, default: 'pytorch')
        - "overwrite": determines whether to overwrite a model if it already exists. If set as True, the old model is replaced with the new model (bool, optional, True|False, default: False)


!!! Failure "__Caution__"
    - The current `UPLOAD MODEL` statement is only available for `Pytorch` based models.

## __3. UPLOAD MODEL Example__

```sql
%%thanosql
UPLOAD MODEL beans_mobilevit
OPTIONS (
    framework="pytorch",
    overwrite=True
    )
FROM "trained_model.pth"
```