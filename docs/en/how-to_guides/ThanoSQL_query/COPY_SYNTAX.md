---
title: COPY
---

# __COPY__

## __1. COPY Statement__

The "__COPY__" statement allows users to create data tables in the thanoSQL DB with their data files, data folders, and dataframe within their workspace.

!!! note "__Supported file types__"
    - csv
    - pkl
    - xls, xlsx, xlsm, xlsb

!!! warning "__Warning__" 
    - The column name of data files and dataframe only allows lowercase letters, numbers and _(underscore) 

## __2. COPY Syntax__

The "__COPY__" statement creates data tables in the thanoSQL DB with their data files, data folders, and dataframe within their workspace.

```sql
%%thanosql
COPY [name of the table in the ThanoSQL DB] 
OPTIONS (
    overwrite=True
) 
FROM  
[absolute path of the data file and data folder or dataframe]
```

!!! note "__Query Details__"
    - The "__OPTIONS__" clause can change the value of a parameter. The meaning of each parameter is as follows:
        - "overwrite" : Overwrite if a model with the same name exists. If True, the existing model is overwritten with the new model. (DEFAULT: False)

## __3. COPY Example__

Using path of the data file: 
```sql
%%thanosql
COPY mytable
OPTIONS (
    overwrite=True
)
FROM "data/example.csv"
```

Using path of the data folder: 

!!! note "__데이터 폴더 COPY 사용법__"
    - If a path of the folder with the images, audios, or videos is given as an input, "__COPY__" clause will translate each image as a row and recreate it as a data table. 

```sql
%%thanosql
COPY mytable
OPTIONS (
    overwrite=True
)
FROM "diet_image_data/"
```


Using a dataframe: 
```sql
%%thanosql
COPY mytable
OPTIONS (
    overwrite=True
)
FROM dataframe
```