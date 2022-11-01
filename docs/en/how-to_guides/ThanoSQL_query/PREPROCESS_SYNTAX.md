---
title: PREPROCESS
---

# __PREPROCESS__

## __1. PREPROCESS Statement__

The "__PREPROCESS__" statement allows users to preprocess their data files(text, image, audio, video) in their workspace using provided methods and create data tables in the thanoSQL DB.

## __2. PREPROCESS Syntax__

```sql
%%thanosql
PREPROCESS TEXT | IMAGE | AUDIO | VIDEO
USING [name_of_the_preprocessing_method]
OPTIONS ([option_values_​​required_for_the_method])
FROM
[absolute_path_of_data_files_in_the_workspace]
```

!!! note "__Query Details__"
    - Specify the options to use for the __PREPROCESS__ statement with an "__OPTIONS__" clause.
        - "table_name" : Table name must be provided as __PREPROCESS__ statement creates a data table at the end of data preprocessing.
        - "column_name" : Preprocessed data will be stored in a column of the data table. This option specify the name of the column.
        - "interval" : Interval for video splitting (DEFAULT:10)

## __3. PREPROCESS Example__

The example below uses split, one of the video preprocessing method, to preprocess .mp4 file and create a data table.

```sql
%%thanosql
PREPROCESS VIDEO
USING split
OPTIONS (
    table_name='video_table',
    column_name='video_path',
    interval=20,
    )
FROM  
"video_files/video_example.mp4"
```
