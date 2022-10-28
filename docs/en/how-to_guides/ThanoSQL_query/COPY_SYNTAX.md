---
title: COPY
---

# __COPY__

## __1. COPY Statement__

The "__COPY__" statement allows users to create data tables in the thanoSQL DB with their data files in their workspace.

!!! note "__Supported file types__"
    - csv
    - pkl
    - xls, xlsx, xlsm, xlsb


## __2. COPY Syntax__


The "__COPY__" statement creates data tables in the thanoSQL DB with their data files in their workspace.

```sql
%%thanosql
COPY [Name of table in the thanoSQL DB] 
OPTIONS (
    overwrite=True
) 
FROM  
[absolute path of data files in the workspace]
```

!!! note "__Note__"    
    - You can use the `COPY` statement to create a data table in the ThanoSQL DB. There is only overwrite option in the `COPY` statement and if not specified, the default value will be set as False.  
    - If the value is True, you can delete an existing data table and create a new data table with the same name in the ThanoSQL DB. Otherwise, you are not allowed to create new data table with the same name. 
