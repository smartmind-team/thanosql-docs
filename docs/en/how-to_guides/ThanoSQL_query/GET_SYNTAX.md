---
title: GET
---

# __GET__

## __1. GET Statement__
The "__GET__" statement allows users to download the lastest ThanoSQL pre-built models and tutorial datasets. 

## __2. GET Syntax__

The "__GET THANOSQL MODEL__" statement downloads the ThanoSQL pre-built models to the user's workspace. 

```sql
%%thanosql
GET THANOSQL MODEL [ThanoSQL_pre-built_model_name] 
OPTIONS (
    overwrite=True
) 
AS 
[custom_model_name]
```

!!! note "__Note__"    
    - You can use the `LIST THANOSQL MODEL` statement to view a list of the ThanoSQL pre-built models.  
    - If you do not include the `AS` clause and `custom_model_name`, pre-built models will be saved with default names. 

!!! note "__Query Details__"
    - The "__OPTIONS__" clause can change the value of a parameter from the default value in an image model. The meaning of each parameter is as follows:
        - "overwrite" : Overwrite if a model with the same name exists. If True, the existing model is overwritten with the new model. (DEFAULT: False) 

The "__GET THANOSQL DATASET__" statement downloads the tutorial datasets to the user's workspace. 

```sql
%%thanosql
GET THANOSQL DATASET [ThanoSQL_dataset_name]
OPTIONS (
    overwrite=True 
)
```

!!! note "__Note__"    
    - You can use the `LIST THANOSQL DATASET` statement to view a list of the ThanoSQL datasets.  
    - Datasets cannot be renamed.

!!! note "__Query Details__"
    - The "__OPTIONS__" clause can change the value of a parameter. The meaning of each parameter is as follows:
        - "overwrite" : Overwrite if a dataset with the same name exists. If True, the existing dataset is overwritten with the new dataset. (DEFAULT: False) 

## __3. GET Example__ 

### __3.1 GET THANOSQL MODEL Example__

```sql
%%thanosql
GET THANOSQL MODEL convnext
OPTIONS (
    overwrite=True
)
AS myconvnext
```

### __3.2 GET THANOSQL DATASET Example__

```sql
%%thanosql
GET THANOSQL DATASET unsplash_data
OPTIONS (
    overwrite=True
)
```
