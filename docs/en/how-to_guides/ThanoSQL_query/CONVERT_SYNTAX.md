---
title: CONVERT
---

# __CONVERT__

## __1. CONVERT Statement__

The "__CONVERT__" statement allows users to convert unstructured data (image|audio|video|text) to vector format using a vectorization algorithm.

## __2. CONVERT Syntax__

```sql
query_statement:
    query_expr

CONVERT USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

## __3. CONVERT Example__

!!! note
    - Examples are specific to one model, and the required option values ​​or the dataset used may differ from model to model. For a detailed description of each model, refer to the [ThanoSQL Model Statement Reference](/en/how-to_guides/reference/#thanosql-model-statement-reference)
    - Because the example only works when a specific model and dataset are present, it may not run normally if copied and used as is.


```sql
%%thanosql
CONVERT USING tutorial_search_clip
OPTIONS (
    image_col='image_path', 
    convert_type='image',
    batch_size=128,
    result_col='convert_result'
    )
AS 
SELECT *
FROM unsplash_data
```

!!! note "AI Models That Can Be Used with '__CONVERT__ Statement'"
    - SimCLR Model - SimCLR
    - CLIP Model - CLIP
    - XCLIP Model - XCLIP
    - SBERT Model - SBERTKo, SBERTEn