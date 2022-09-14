---
title: GET
---

# __GET__

## __1. GET Syntax Overview__

Users can utilize "__GET__" query syntax to download lastest ThanoSQL pre-built models and tutorial datasets. 


## __2. GET Syntax__

"__GET THANOSQL MODEL__" query syntax downloads ThanoSQL pre-built models to one's workspace. 

```sql
%%thanosql
GET THANOSQL MODEL [ThanoSQL pre-built model name] 
OPTIONS (
    overwrite=True
) 
AS 
[Custom model name]
```

!!! note "__Note__"    
    One can use the LIST THANOSQL MODEL syntax to view a list of ThanoSQL pre-built models. There is only overwrite option in GET syntax and if not specified, the default value of the overwrite status would be False. If you do not include AS and custom model name when using GET syntax, pre-built models will be saved as the default names. 


"__GET THANOSQL DATASET__" query syntax downloads tutorial datasets to one's workspace. 

```sql
%%thanosql
GET THANOSQL DATASET [ThanoSQL dataset name]
OPTIONS (
    overwrite=True 
)
```

!!! note "__Note__"    
    One can use the LIST THANOSQL DATASET syntax to view a list of ThanoSQ datasets. There is only overwrite option in GET syntax and if not specified, the default value of the overwrite status would be False. Datasets cannot be renamed. 

