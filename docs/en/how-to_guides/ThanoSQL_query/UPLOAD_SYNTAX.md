---
title: UPLOAD MODEL
---

# __UPLOAD MODEL__

## __1. UPLOAD  MODEL Syntax Overview__

Users can utilize "__UPLOAD MODEL__" query syntax to transfer their own models to ThanoSQL to be used within ThanoSQL environment. 

## __2. UPLOAD MODEL syntax__
```sql
%%thanosql
UPLOAD MODEL [user defined model name] 
OPTIONS (
    overwrite=True, 
    framework=[model framework]
    ) 
FROM [path of a model to upload]
```

!!! note "__Note__"     
    OPTIONS in the UPLOAD MODEL syntax allows you to specify the overwrite status and model framework. If the overwrite status is not specified, the default value is False. The model framework must be specified directly by the user when using the UPLOAD syntax. Currently, the available framework is Pytorch only.
    
!!! Failure "__Caution__"
    The current UPLOAD MODEL syntax is only available for Pytorch based models.
