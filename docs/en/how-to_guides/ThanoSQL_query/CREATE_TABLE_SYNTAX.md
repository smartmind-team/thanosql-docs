---
title: CREATE TABLE
---

# __CREATE TABLE__

## __1. CREATE TABLE Query Syntax overview__

Using the syntax "__CREATE TABLE__", users can create a data table that converts unstructured data (image, audio, video, etc.) into vector formats using a quantization algorithm.

## __2. CREATE TABLE Syntax__

```sql
CREATE TABLE [Custom_data_table_name]
USING [AI_model_to_use]
OPTIONS (overwrite=True) -- default:False
FROM [data_set_to_use]
```

!!! note "__Query Details__"
    - Specify the options to use for __CREATE TABLE__ with the query syntax "__OPTIONS__".
        - "overwrite" : Set whether or not a data set with the same name can be overwritten when it exists on the DB. If True, the existing dataset is changed to the new dataset (True|False, DEFAULT: False)

## __3. CREATE TABLE Syntax example__

The example below uses an attribute extraction AI model called 'data/thanosAlgo/image_search/junyoung_test/' `Color_descriptor` to create image files in the path in a data table named `color_descriptor_table_test` in the ThanoSQL DB.

```sql
%%thanosql
CREATE TABLE color_descriptor_table_test
USING Color_Descriptor
OPTIONS (
    data_type='image',
    file_type=['.jpg'],
    overwrite = True
    )
FROM 'data/thanosAlgo/image_search/junyoung_test/'
```
