---
title: CONVERT
---

# __CONVERT__

## __1. CONVERT Statement__

The "__CONVERT__" statement allows users to convert unstructured data such as images, videos, and audio to vector format using a vectorization algorithm and append it to the given data table.

## __2. CONVERT Syntax__

```sql
CONVERT USING [AI_model_to_use]
OPTIONS ([option_values_​​required_for_vectorization])
AS
[dataset_to_use]
```

!!! note "__Query Details__"
    - The "__OPTIONS__" clause can change the value of a parameter. The meaning of each parameter is as follows:
        - "table_name" : The name of the new table to be created.

## __3. CONVERT Examples__

!!! note
    - Examples are specific to one model, and the required option values ​​or the dataset used may differ from model to model. For a detailed description of each model, refer to the [ThanoSQL Pre-built Model Statement Reference](/en/how-to_guides/reference/#thanosql-pre-built-model-statement-reference)
    - Since the example works only when a specific model and dataset exist, it may not run normally when copied and used as it is.

### __3.1 Image vectorization using `CLIP` model__

```sql
%%thanosql
CONVERT USING clip
OPTIONS (
    image_col="image_path",
    table_name="unsplash_data", 
    batch_size=128
    )
AS 
SELECT * 
FROM unsplash_data
```

### __3.2 Image vectorization using `SimCLR` model__

```sql
%%thanosql
CONVERT USING simclr
OPTIONS (
    table_name= "convert_result_table",
    image_col="image_path"
    )
AS 
SELECT * 
FROM mnist_test
```