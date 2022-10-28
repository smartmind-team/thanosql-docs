---
title: COPY
---

# __COPY__

## __1. COPY Statement__

The "__COPY__" statement allows users to create/copy data tables in the ThanoSQL workspace DB with their data files.

!!! note "__Supported file types__"
    - csv
    - pkl
    - xls, xlsx, xlsm, xlsb

!!! warning "__Warning__" 
    - The column name of data files only allows lowercase letters, numbers and _(underscore) 
    - ThanoSQL only supports pkl files created by using pandas 

## __2. COPY Syntax__


The "__COPY__" statement creates data tables in the ThanoSQL workspace DB with their data files in their workspace.

```sql
%%thanosql
COPY [Name_of_table_in_the_ThanoSQL_DB] 
OPTIONS (
    overwrite=True
) 
FROM  
[absolute_path_of_data_files_in_the_workspace]
```

!!! note "__Query Details__"
    - Specify the options to use for the __COPY__ statement with an "__OPTIONS__" clause.
        - "overwrite" : Overwrite if a dataset with the same name exists. If True, the existing dataset is overwritten with the new dataset. (True|False, DEFAULT: False)
