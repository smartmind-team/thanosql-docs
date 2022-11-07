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

!!! warning "__Warning__" 
    - The column name of data files only allows lowercase letters, numbers and _(underscore) 

## __2. COPY Syntax__

The "__COPY__" statement creates data tables in the thanoSQL DB with their data files in their workspace.

```sql
%%thanosql
COPY [Name_of_table_in_the_thanoSQL_DB] 
OPTIONS (
    overwrite=True
) 
FROM  
[absolute_path_of_data_files_in_the_workspace]
```

!!! note "__Query Details__"
    - The "__OPTIONS__" clause can change the value of a parameter. The meaning of each parameter is as follows:
        - "overwrite" : Overwrite if a model with the same name exists. If True, the existing model is overwritten with the new model. (DEFAULT: False)

## __3. COPY Example__

```sql
%%thanosql
COPY mytable
OPTIONS (
    overwrite=True
)
FROM "data/example.csv"
```